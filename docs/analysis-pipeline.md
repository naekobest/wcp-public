# Analysis Pipeline

## Overview

When a report is submitted, an `AnalyzeRaidJob` is dispatched to the appropriate queue tier. The job runs the **AnalysisPipeline**, which orchestrates all active analysis services for the detected expansion.

```
AnalyzeRaidJob
  -> AnalysisPipeline::run(report)
      -> Load active services for this expansion
      -> Sort by priority
      -> QueryPlanner: merge DataRequirements -> minimal GraphQL queries
      -> WarcraftLogsClient: execute queries -> ReportContext
      -> foreach service: analyze(ReportContext) -> persist result
      -> Report status -> completed
```

## QueryPlanner: API Efficiency

Each analysis service declares what WCL data it needs via a `DataRequirements` value object. The **QueryPlanner** collects requirements from all active services and merges them into the minimum number of GraphQL queries before any API call is made. No service makes direct API calls.

WCL queries are tiered by cost:

**Tier 1: Metadata (cheap, 1 request)**
Fight list, actor roster with role assignments (tank/healer/DPS), zone/expansion info, current API budget. Always fetched first. Services that need role-segmented player data (e.g. `DpsService`) declare `needsPlayers()` in their `DataRequirements` and receive healer and tank name sets via `ReportContext` without an additional API call.

**Tier 2: Aggregated Tables (medium, 1 to 2 requests)**
Pre-computed server side summaries: damage done, healing, damage taken, deaths, buff uptimes, player gear/talents. Covers most Performance and Overview services without touching raw events.

By default, Tier 2 table queries cover boss fights only. Services that need trash pull data declare `needsTrashData` in their `DataRequirements`. The QueryPlanner then issues a second pass of table queries scoped to trash fights — including a dedicated buff data build for trash windows — and makes the results available via `ReportContext::getTrashFights()`. This keeps the default query count minimal while enabling full boss / trash / total segmentation across all categories (Performance, Execution, and Buffs).

**Tier 3: Raw Events (expensive, N requests)**
Spell-level event streams filtered by ability ID or event type. Only fetched when a service explicitly requires it. Examples include interrupt events, world buff combatant info, armor debuff application and removal events, and Ignite tick sequences.

The budget is rechecked between Tier 2 and Tier 3. If the app budget has been depleted by other concurrent jobs, Tier 3 queries are deferred.

## Analysis Service Interface

Every analysis service implements the same interface:

```php
interface AnalysisServiceInterface
{
    public function serviceKey(): string;
    public function category(): AnalysisCategory;       // Execution|Preparation|Performance|Buffs|Overview
    public function resultScope(): ResultScope;         // Raid|Boss|Player|BossPlayer
    public function supportedExpansions(): array;       // e.g. ['vanilla', 'tbc']
    public function requiredData(): DataRequirements;   // declares needed WCL fields
    public function priority(): int;                    // lower = runs first
    public function analyze(ReportContext $context): array;
}
```

Services are registered per expansion in config. Adding analysis for a new mechanic means implementing one class and registering it, nothing else changes.

## Expansion Configuration

Each expansion has a config file defining:

- Which services are active
- Spell IDs for tracked abilities (sunder armor, expose armor, world buffs, etc.)
- Analysis thresholds (e.g. "interrupt rate below 80% flagged")
- Difficulty modes (Vanilla = Normal only; Retail = Normal/Heroic/Mythic)

This means the same `InterruptService` works for every expansion. The expansion config supplies the interrupt spell IDs and the threshold that constitutes good interrupt coverage.

## Result Storage

Analysis results are stored in four scoped tables:

| Scope | Table | Key |
|-------|-------|-----|
| Raid-wide | `raid_analysis_results` | `raid_id` |
| Per boss | `boss_analysis_results` | `raid_boss_id` |
| Per player (raid) | `player_analysis_results` | `raid_id + player_id` |
| Per boss + player | `boss_player_analysis_results` | `raid_boss_id + player_id` |

Results are written once and resubmitting the same report is a no-op. The `data` column is JSONB, which allows each service to store whatever structure it needs without requiring migrations when services evolve.

## Metrics

Each analysis service persists raw metrics (DPS numbers, uptimes, counts, durations) rather than derived scores. The frontend surfaces these metrics directly — Deaths, DPS/HPS, Buff Uptimes, Consumable compliance, Interrupts, Dispels — so users see exactly what happened without an intermediate scoring layer.

### Performance: Class-Based Medians

DPS and Healing services compare each player's output against the median for their class within the same raid. This avoids punishing players for playing lower-DPS specs and avoids rewarding players for simply being in a raid with lower overall output.

Pets and NPCs are automatically filtered from rankings using the `knownPlayerNames` set derived from the WCL roster.
