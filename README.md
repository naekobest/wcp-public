# WarcraftPulse

> Raid log analysis for WoW Classic, built for guild leads and powered by WarcraftLogs.

[![Status](https://img.shields.io/badge/status-beta-orange)](https://github.com/naekobest/wcp-public)
[![Stack](https://img.shields.io/badge/stack-Laravel%2012%20%7C%20React%20%7C%20PostgreSQL-blue)](docs/architecture.md)

WarcraftPulse ingests your WarcraftLogs report and runs a structured analysis pipeline across four categories: **Execution**, **Preparation**, **Performance**, and **Buffs**. Every player is tracked with concrete metrics — Deaths, DPS/HPS, Buff Uptimes, Consumable Usage, Interrupts, Dispels — so guild leaders can see exactly what happened without opening a single WCL tab.

No spreadsheets. No manual log scrubbing. Submit a WCL URL, wait a few seconds, get a full breakdown.

## Why Not Just Use WarcraftLogs?

WarcraftLogs is excellent for raw data: damage meters, cast timelines, event logs. What it doesn't do is tell you *what matters* and *who's responsible*. WarcraftPulse answers:

- Did your tanks keep Sunder/Expose Armor at full stacks all fight?
- Which healers were world buffed, which weren't?
- Who used their engineering items on cooldown?
- Are your DPS players running the right resistance gear?

WarcraftLogs shows you the events. WarcraftPulse surfaces what matters.

## What Gets Analyzed

### Execution

Mechanics that directly affect raid performance if missed.

- **Interrupts**: tracking rate vs. available windows per player
- **Armor debuff uptime**: Sunder Armor / Expose Armor coverage by tanks
- **Boss debuffs**: Curse of Recklessness, Curse of Elements, Faerie Fire uptime
- **Death analysis**: avoidable vs. unavoidable, per boss, per player
- **Ignite tracking**: per-instance Ignite breakdown using WCL debuff bands — duration, max tick, contributing spells per caster, uptime per boss. Grief data from the griefing service is displayed inline per Ignite instance with warning badges.
- **Ignite griefing**: stack-tracking state machine detects wrong spells during build phase (< 5 stacks) and maintenance phase (5 stacks). Per-instance details in the Ignite tab, raid-wide overview in a dedicated tab.
- **Major cooldown usage**: per-player tracking of throughput CDs (Death Wish, Adrenaline Rush, Arcane Power), utility CDs (Power Infusion, Innervate) with cast targets, and defensive CDs. Throughput cooldowns are scored by actual vs. expected usage per boss fight.

### Preparation

What players bring to the raid before the first pull.

- **World buffs**: who had which buffs at pull, compliance rate per boss
- **Consumables**: flasks, elixirs, food buffs per player and role
- **Gear quality**: enchants, gems, item level relative to content
- **Professions**: relevant crafted gear and engineering presence
- **Resistance gear**: correct sets equipped for resistance check fights

### Performance

How effectively players use their toolkit during combat.

- **DPS**: per-player damage per second scored against the class median within the same raid — a Mage doing well relative to other Mages scores higher than one carried by raid composition; healers and tanks excluded
- **Healing**: per-healer HPS and overhealing scored against the class median; overhealing percentage remains a primary input to the Performance category score
- **Damage taken**: per-player damage scored relative to raid average (tanks excluded), broken down by trash, boss, and full clear
- **Engineering**: Goblin Sapper, grenades, on use trinkets tracked
- **Trinket usage**: on cooldown usage rate for major DPS/healing trinkets
- **Drums of Battle**: coverage and overlap analysis for Leatherworkers

### Buffs

Raid-wide buff infrastructure and uptime.

- **Raid buff uptime**: Blessing of Kings, Mark of the Wild, etc.
- **Combat buffs**: Bloodlust/Heroism coverage per fight phase
- **Player buff coverage**: who received which buffs and for how long

## Metrics

WarcraftPulse surfaces raw, concrete metrics instead of abstract scores:

- **Deaths** — per-player, per-boss, avoidable vs. unavoidable
- **DPS / HPS** — class-median comparison with trend charts
- **Buff Uptimes** — world buffs, raid buffs, consumables
- **Consumables** — flask, elixir, food compliance per player
- **Interrupts** — response rate and coverage
- **Dispels** — per-type (Magic/Poison/Disease/Curse) leaderboard
- **Active Time** — engagement percentage per player

Reports show animated count-up summaries on completion. Character pages include DPS/HPS trend area charts and 60-day activity grids. Dashboard stats highlight Boss Kills and Characters Tracked.

## Achievements

45 achievements across 8 categories track your journey on the platform. Tiered achievements (I through IV) unlock progressively as you submit more reports, hit gameplay milestones (zero deaths, full flask compliance, veteran raider status), and explore different classes and expansions. Legendary one-time achievements reward rare accomplishments like being an early adopter.

Pin up to 3 achievements to your public profile as a showcase. Achievement progress syncs daily.

## Public Profiles

Every user gets a public profile at `/u/{username}` showing their pinned achievement showcase and visible achievements. Profile visibility and achievement display are configurable in settings.

## GDKP Module

WarcraftPulse includes a full GDKP gold distribution system for raid organizations. Officers create organizations, configure raid templates with role brackets and cut formulas, run sign-ups (on the website or through a Discord bot), draft gold sheets, calculate payouts, and publish tamper-evident public sheets.

Key capabilities:

- **Organization management** with 7 granular roles (Owner, Host, Officer, Raid Lead, Auctioneer, Banker, Player) and 26 configurable permissions
- **Bonus templates** with 3 distribution modes: flat percentage, performance-based (DPS + Heal pools from WCL data), and performance + manual assignment pools — live preview sidebar and pot flow visualization
- **Deduction templates** with setup wizard, config-based presets (53 rules per severity variant), and live preview
- **Assignment templates** with drag-and-drop row management, separator rows, class-colored character names, and autocomplete
- **Sign-ups** via web or Discord bot with role/spec selection, character linking, and self-withdrawal
- **Gold sheets** with boss assignments, assignment matrix, and template assignment tabs (Bonuses, Deductions, Settings)
- **Cut calculation** with role brackets, performance pools, assignment groups, and configurable org cuts
- **Public sheets** with short URLs, integrity hash verification, and anonymous cut distribution histograms
- **Treasury** with auto-booking, cross-sheet payouts, and CSV export
- **Gargul & Softres** loot import (including CSV format)
- **Recurring raids** with auto-open-signups
- **Default price lists** auto-seeded on organization creation

Finalized gold sheets are always public for transparency.

## Desktop Uploader

A companion Windows app that watches your WoW combat log directory and automatically uploads logs to warcraftpulse.com. Supports auto-watch, manual upload, duplicate detection, upload history, and secure token storage via Windows DPAPI.

Source: [warcraftpulse-uploader](https://github.com/naekobest/warcraftpulse-uploader)

## Discord Bot

A lightweight sidecar bot that posts raid event embeds in Discord channels and handles sign-up interactions via buttons, select menus, and modals. The bot is stateless — all business logic lives in the Laravel backend.

Source: [warcraftpulse-discord-bot](https://github.com/naekobest/warcraftpulse-discord-bot)

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | Laravel 12, PHP 8.4 |
| Frontend | React 19, Inertia.js v2, TypeScript |
| Database | PostgreSQL (partitioned by expansion) |
| Queue | Redis + Laravel Horizon |
| Auth | WarcraftLogs OAuth 2.0 |
| Payments | Stripe (subscription tiers) |
| API | WarcraftLogs GraphQL v2 + REST (upload) |
| Desktop | .NET 8, WinForms (Windows uploader) |
| Discord | discord.js, Node.js (sidecar bot) |

See [Architecture Overview](docs/architecture.md) for more detail.

## Expansion Support

| Expansion | Status |
|-----------|--------|
| WoW Classic (Vanilla) | Beta |
| The Burning Crusade Classic | Planned |
| Wrath of the Lich King Classic | Planned |
| Cataclysm Classic | Planned |
| Mists of Pandaria Classic | Planned |
| Season of Discovery | Planned |
| Retail | Planned |

Analysis services are expansion-aware. Each expansion has its own set of enabled checks, spell IDs, and scoring thresholds.

## Status

WarcraftPulse is currently in **Beta**. Classic Vanilla is the first supported expansion. If you want to follow development, watch this repo or join the Discord (link coming soon).

## Learn More

- [Architecture Overview](docs/architecture.md): stack, queue design, WCL API integration
- [Analysis Pipeline](docs/analysis-pipeline.md): how the scoring engine works
- [Analysis Services](docs/services.md): full list of implemented services with expansion support
- [Per-Service Documentation](docs/services/index.md): technical analysis, scoring formulas, and WCL data dependencies for each service
- [GDKP Module](docs/gdkp.md): organization management, gold sheets, sign-ups, and cut calculation
- [Desktop Uploader](https://github.com/naekobest/warcraftpulse-uploader): Windows app for automatic log uploads
- [Discord Bot](https://github.com/naekobest/warcraftpulse-discord-bot): sidecar bot for raid sign-ups in Discord
- [Changelog](CHANGELOG.md): development history and recent changes
- [Roadmap](docs/roadmap.md): planned features, expansion timeline
- [Contributing](CONTRIBUTING.md): how to suggest features or new analysis checks
