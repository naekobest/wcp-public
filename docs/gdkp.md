# GDKP Module

Gold Distribution Keeps People (GDKP) raids distribute loot by gold auction. WarcraftPulse provides a full management system for GDKP organizations, from sign-ups to public payout transparency.

## Organizations

Each GDKP operation is managed through an organization. Organizations have:

- **Officers** with role-based permissions (owner, officer, banker)
- **Members** linked to WarcraftPulse accounts or Discord
- **Treasury** with a running balance, auto-booked org cuts, and manual ledger entries
- **Templates** for reusable raid configurations
- **Presets** for saving and applying template snapshots

Organizations are listed on the public GDKP Hub unless opted out.

## Raid Templates

Templates define the rules for a GDKP run:

- **Role brackets** — how the cut pool is split across tanks, healers, and DPS
- **Special roles** — additional cut modifiers for specific assignments (main tank, loot master, etc.)
- **Bonus caps** — maximum bonus in basis points, configurable per org and per template
- **Boss assignments** — which players are assigned to which boss kills for slot bonuses
- **Deduction rules** — penalties applied to the cut based on configurable criteria
- **WCL rules** — deductions or bonuses tied to WarcraftLogs analysis data (debuff uptime, deaths, DPS percentile). Rules are sourced from admin-managed presets per expansion and raid zone. Rules are snapshotted on review transition so they remain immutable after lock.
- **Minimum bids** — per-item floor prices

Templates can be saved as presets for reuse across raids. Eight system presets ship by default covering different raid styles and organization sizes.

## Sign-ups

Players sign up for raids through the website or the Discord bot:

- **Website sign-ups** — players pick a role and spec from the raid page
- **Discord bot sign-ups** — players click a button on the raid event embed in Discord, select a role via dropdown, and optionally enter a character name via modal if their Discord account isn't linked yet
- **Discord signup linking** — when a player connects their Discord account on the website, their previous bot sign-ups are automatically linked to their WarcraftPulse account. Officers can also manually link/unlink Discord users.
- **Auto-open signups** — a scheduled command opens sign-ups for upcoming raids automatically
- **Recurring raids** — officers can create raid series that repeat on a schedule

## Gold Sheets

After the raid, officers fill out the gold sheet:

- **Items tab** — log items won with buyer, price, and optional Wowhead tooltip
- **Assignments tab** — visual assignment matrix mapping players to boss slots
- **Results tab** — calculated cuts per player based on template rules, role brackets, boss-slot bonuses, and deductions
- **Revisions** — every sheet change is tracked with a full revision history

Sheets go through a lifecycle: Draft → Review → Finalized → Archived. WCL rules and template configuration are snapshotted on review transition. Cut calculations are deterministic given the same inputs.

## Cut Calculation

The cut calculation engine evaluates:

1. **Total pot** — sum of all item prices minus org cut percentage
2. **Role brackets** — the distributable pool is split according to configured tank/healer/DPS ratios
3. **Special roles** — additional modifiers for designated assignments
4. **Boss-slot bonuses** — players assigned to specific boss kills receive slot bonuses up to the configured cap
5. **Deductions** — rule-based penalties subtracted from individual cuts
6. **WCL-backed rules** — bonuses or deductions derived from analysis data (if configured)

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

- **Gargul addon** — import loot data from the Gargul WoW addon (parse, preview, bulk import)
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
