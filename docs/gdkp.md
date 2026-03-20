# GDKP Module

Gold Distribution Keeps People (GDKP) raids distribute loot by gold auction. WarcraftPulse provides a full management system for GDKP organizations, from sign-ups to public payout transparency.

## Organizations

Each GDKP operation is managed through an organization. Organizations have:

- **7 granular roles**: Owner, Host, Officer, Raid Lead, Auctioneer, Banker, Player — each with configurable permissions (26 discrete permissions per organization)
- **Members** linked to WarcraftPulse accounts or Discord
- **Treasury** with a running balance, auto-booked org cuts, and manual ledger entries
- **Templates** for reusable raid configurations
- **Presets** for saving and applying template snapshots

Organizations are listed on the public GDKP Hub unless opted out.

## Raid Templates

Templates define the rules for a GDKP run. There are three template types:

### Bonus Templates

Bonus templates control how the cut pool is distributed. Three modes are available:

- **Flat** — traditional percentage-based distribution across all raiders
- **Performance (DPS + Heal)** — two pools distributed proportionally by WCL damage and healing data, with an equal-split remainder
- **Performance (DPS + Heal + Misc)** — adds a third officer-assigned pool for utility roles (tanks, buffs, etc.)

A 3-step setup wizard (Preset → Mode → Management Cut) guides template creation. **Unified assignment groups** replace the previous role brackets, special roles, and boss assignments. Officers define named groups with configurable weight shares. **Management cut subdivision** splits the org cut into named portions (e.g. "Guild Bank", "Host Fee"). A **live preview sidebar** recalculates the full pot distribution in real time, and a **pot flow visualization** shows how gold moves through each stage.

### Deduction Templates

Deduction templates define penalties applied to individual cuts. Each template stores its rules as embedded JSON, making templates fully self-contained.

A setup wizard (Name → Zone → Severity) auto-populates rules from config-based presets — 53 rules per severity variant (Light, Standard, Strict), covering consumables, world buffs, enchants, debuff uptime, and more. Officers can toggle rules, adjust gold values, and add custom rules. A **live preview** shows the impact across a sample raid roster.

### Assignment Templates

Assignment templates map players to boss slots for cut bonuses. The editor supports:

- **Row operations**: delete, drag-and-drop reorder, and duplicate
- **Separator rows**: labeled dividers (Tanks, Healers, Melee DPS, Ranged DPS, Caster) for visual grouping
- **Class-colored character names**: names render in WoW class colors
- **Autocomplete**: character name suggestions from organization signup history
- **Keyboard navigation**: Tab, Enter, and Arrow keys between cells

Templates can be saved as presets for reuse across raids. Eight system presets ship by default covering different raid styles and organization sizes.

## Sign-ups

Players sign up for raids through the website or the Discord bot:

- **Website sign-ups** — players pick a role and spec from the raid page
- **Discord bot sign-ups** — players click a button on the raid event embed in Discord, select a role via dropdown, and optionally enter a character name via modal if their Discord account isn't linked yet
- **Discord signup linking** — when a player connects their Discord account on the website, their previous bot sign-ups are automatically linked to their WarcraftPulse account. Officers can also manually link/unlink Discord users.
- **Self-withdrawal** — players can withdraw from a raid signup with a mandatory reason. Officers are notified when confirmed spots open and the standby queue advances. Players can re-signup after withdrawal.
- **Auto-open signups** — a scheduled command opens sign-ups for upcoming raids automatically
- **Recurring raids** — officers can create raid series that repeat on a schedule

## Gold Sheets

After the raid, officers fill out the gold sheet:

- **Items tab** — log items won with buyer, price, and optional Wowhead tooltip
- **Assignments tab** — visual spreadsheet-like assignment matrix mapping players to boss slots, with separator rows and class colors
- **Bonuses tab** — active bonus config display with live gold distribution preview
- **Deductions tab** — editable deduction rules with live preview across the roster
- **Settings tab** — template selectors for swapping bonus and deduction templates on draft sheets, with confirmation on re-apply
- **Results tab** — calculated cuts per player based on template rules, assignment groups, performance pools, and deductions
- **Revisions** — every sheet change is tracked with a full revision history

Sheets go through a lifecycle: Draft → Review → Finalized → Archived. Template configuration is snapshotted on review transition. Cut calculations are deterministic given the same inputs.

## Cut Calculation

The cut calculation engine evaluates:

1. **Total pot** — sum of all item prices minus management cut (subdivided into named splits)
2. **Performance pools** (if configured) — DPS and Heal sub-pools distributed proportionally by WCL data
3. **Assignment pools** (if configured) — officer-defined groups with weight shares
4. **Equal split** — remainder after performance and assignment pools, divided equally
5. **Deductions** — rule-based penalties subtracted from individual cuts

In flat mode, the distributable pool is split across all raiders equally after management cut and deductions. Config snapshots are versioned (v1 flat, v2 performance) for backward compatibility with existing finalized sheets.

Results are shown per player in the Results tab with a breakdown of each component.

## Public Sheets

Finalized sheets are published at a short public URL (`/g/s/{short_id}`). The public view includes:

- Per-player cut amounts (without revealing internal calculation details)
- Anonymous cut distribution histogram showing the spread across all participants
- Integrity hash verification — a SHA-256 hash of the sheet data that can be independently verified
- Item list with prices

No login required to view a public sheet.

## Treasury

Each organization maintains a treasury:

- **Auto-booking** — the org cut is automatically booked as a credit on sheet finalization
- **Manual entries** — officers can add manual debits or credits with notes
- **Balance card** — running balance displayed on the org dashboard
- **Banker role** — designated officer with treasury management permissions
- **Cross-sheet payouts** — pending distributions grouped by character across all finalized sheets with bulk "mark all paid"
- **CSV export** — download finalized/archived sheets with participant data, items, and payout breakdown

## Integrations

- **Gargul addon** — import loot data from the Gargul WoW addon (parse, preview, bulk import) with CSV format support
- **Softres** — CSV import with reserve badges displayed on participants
- **Discord bot** — raid event embeds with button-based sign-ups, role selection, and character name modals
- **Discord verification** — optional requirement for sign-ups to have a linked Discord account
- **RaidHelper** — sync integration for cross-posting events

## Discord Bot

The Discord bot is a separate Node.js application ([source](https://github.com/naekobest/warcraftpulse-discord-bot)) that handles raid sign-up interactions:

- Posts and updates rich embeds for raid events in configured Discord channels
- Handles button clicks (sign up, cancel, change status)
- Renders role/spec selection via select menus
- Prompts for character name via modal for unlinked Discord users
- Auto-updates embeds when sign-ups close, status changes, or raids are deleted
- Cleans up embeds when raids or series are deleted

The bot is stateless. All business logic runs in the Laravel backend via an internal API. Officers configure bot channels from the organization settings page.
