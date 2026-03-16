# Architecture Overview

## Stack

WarcraftPulse is a server rendered SPA built on **Laravel 12** (PHP 8.4) with **Inertia.js v2** and **React 19**. The backend handles all business logic, queue management, and WarcraftLogs API communication. The frontend is a TypeScript React app rendered server side via Inertia, with no separate API, no JWT, and no state management library needed.

**Database:** PostgreSQL with LIST partitioning by game version. High-volume tables (raids, players, analysis results) are partitioned per expansion, so queries for Vanilla data never touch MoP partitions and vice versa. Analysis result rows are written once. There is no reanalysis and submitting the same report twice is a no-op.

**Auth:** WarcraftLogs OAuth 2.0 (Authorization Code + PKCE). No email/password accounts. Your WCL identity is your identity. OAuth tokens are stored encrypted at rest.

## Queue Architecture

Analysis jobs are expensive. They make multiple GraphQL API calls and run a pipeline of around 15 services per report. To keep Premium users fast regardless of Free tier load, there are four dedicated worker pools:

| Tier | Queue | Workers (prod) | SLA |
|------|-------|----------------|-----|
| Free | `free` | 5 | < 10 min |
| Premium Basic | `premium` | 10 | < 3 min |
| Premium Pro | `premium-pro` | 15 | < 1 min |
| System | `default` | 2 | |

Free and Premium jobs **never share workers**. A spike in free tier submissions cannot delay paying users.

## WarcraftLogs API Budget

WCL's v2 API has a per hour point budget. WarcraftPulse manages this in three tiers:

1. **User has their own WCL v2 API key**: uses their own budget, dispatched to the highest priority queue for their subscription tier
2. **No own key, app budget available**: uses the shared app budget, normal queue priority
3. **No own key, app budget above 70%**: routed to the budget constrained queue (longer wait, clearly communicated to the user)

Users without their own API key see a persistent prompt to create one at WarcraftLogs settings. It takes 30 seconds and unlocks better queue priority.

**Rate limit handling:** On a WCL 429, the job releases itself back to the queue with the exact retry after delay and no tries are decremented. Jobs are configured with a 2-hour deadline to safely outlast WCL's 3600-second budget reset cycle without being marked as failed.

## Expansion Detection

WCL report URLs contain a report code but no expansion information. The expansion is always determined from the **Zone ID** returned by the WCL API, not from any URL slug or user input. Zone IDs are mapped to expansions in a config file. This means "Classic" (a slug that changes meaning each year as the progression server moves forward) always resolves correctly.

## Authentication Flow

1. User clicks "Login with WarcraftLogs"
2. OAuth Authorization Code + PKCE flow
3. Access token and refresh token stored encrypted
4. On first login: character sync job dispatched automatically
5. Viewing reports: fully public, no auth required
6. Submitting reports: requires auth

Personal WCL API keys (for own budget usage) are stored separately, also encrypted.

## Achievement System

WarcraftPulse tracks 45 achievements across 8 categories. Achievements are either tiered (I through IV, unlocking progressively) or one-time legendary milestones. Progress is synced daily via a scheduled job that runs SQL predicate-based threshold checks against each user's cumulative data.

The system is designed for efficiency: the daily sync job evaluates all achievement checkers for all users in a single scheduled run rather than reacting to individual events. Each checker is a SQL predicate, so the sync scales with the number of users without additional API calls or complex event processing.

Users can pin up to 3 unlocked achievements to their public profile as a showcase. Achievement visibility is toggleable globally in appearance settings. Tier colors match WoW's item quality scale (Common through Legendary).

## Public Profiles

Each user has a public profile at `/u/{username}`. The profile displays:

- Pinned achievement showcase (up to 3 achievements)
- All visible achievements grouped by category
- Basic account information

Profile visibility and achievement display are user-configurable. The profile page is publicly accessible without authentication.

## Upload API

An authenticated REST API endpoint accepts pre-parsed combat log data from the desktop uploader. Users generate API tokens at **Settings → API** (gated behind the Pro subscription tier). Tokens are scoped to upload-only operations and managed via Laravel Sanctum with per-tier rate limits.

The endpoint validates the parsed payload structure, deduplicates by file hash, creates a combat log snapshot, and dispatches the same analysis pipeline used for WCL-sourced reports. A server-side combat log parser extracts structured data from raw WoW log files, including `COMBATANT_INFO` gear and aura data for Preparation scoring.

## Discord Bot Architecture

The Discord bot is a separate Node.js process (discord.js) that runs as a sidecar alongside the Laravel backend. It connects to Discord Gateway, listens for button, select menu, and modal interactions on raid event embeds, and forwards them to an internal Laravel API endpoint.

The bot is stateless. It has no database, no user sessions, and no business logic. Every interaction is forwarded to the Laravel API with the Discord user ID, guild ID, channel ID, and interaction payload. The Laravel backend decides what to respond with (ephemeral message, follow-up select menu, or modal). The bot renders the response and sends it back to Discord.

Embeds are posted and updated by Laravel queue jobs (`PostEventEmbedJob`, `UpdateEventEmbedJob`, `DeleteDiscordEmbedsJob`) using the bot token for API calls. The bot's Gateway connection only handles incoming interactions.

## GDKP Module

The GDKP module is a multi-tenant system where each organization manages its own raids, templates, members, and gold sheets. Data is scoped per organization with officer-level authorization via an `AuthorizesOfficer` trait.

Key architectural decisions:

- **Template presets** allow reusable raid configurations. System presets are seeded per expansion. Org presets are user-created.
- **WCL rule templates** tie deduction/bonus rules to raid zones and bosses. Rules are snapshotted into the sheet config on review transition so they're immutable once locked.
- **Cut calculation** runs in a service layer with role brackets, special roles, boss-slot bonuses, and configurable caps. Results are deterministic given the same inputs.
- **Public sheets** use short IDs for shareable URLs and include an integrity hash for tamper evidence.
- **Treasury** auto-books the org cut on sheet finalization and supports manual ledger entries for corrections.

The Discord bot handles sign-up interactions for GDKP raids. Players who sign up through Discord are tracked by `discord_user_id` and automatically linked when they connect their Discord account on the website.
