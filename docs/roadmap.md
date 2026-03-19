# Roadmap

## Expansion Support

Classic progression servers move forward over time and WarcraftPulse is designed to keep up. Each expansion requires its own set of analysis services, spell IDs, thresholds, and enabled checks.

| Expansion | Priority | Status |
|-----------|----------|--------|
| WoW Classic (Vanilla) | 1 | Beta |
| Season of Discovery | 2 | In Progress |
| The Burning Crusade Classic | 3 | In Progress |
| Wrath of the Lich King Classic | 4 | Planned |
| Cataclysm Classic | 5 | Planned |
| Mists of Pandaria Classic | 6 | Planned |
| Retail | 7 | Planned |

## Feature Roadmap

### Recently Shipped

- **GDKP module (public beta)** — organization management, template presets, WCL rule templates, sign-ups (web + Discord bot), gold sheets with boss assignments and assignment matrix, cut calculation with role brackets and bonus caps, treasury with auto-booking and cross-sheet payouts, CSV export, public sheets with integrity hash, Gargul and Softres import, recurring raids, public/player/management sidebar modes, assignment template auto-fill with drag-and-drop, bonus template live preview, CSV pricelist import/export, raid soft-delete
- **Desktop uploader** — Windows app (.NET 8) for automatic combat log upload with auto-watch, duplicate detection, upload history, and DPAPI token encryption ([source](https://github.com/naekobest/warcraftpulse-uploader))
- **Discord bot** — sidecar Node.js bot for raid event embeds and button-based sign-ups in Discord, with automatic signup linking on account connect, class-based embed grouping with Application Emojis ([source](https://github.com/naekobest/warcraftpulse-discord-bot))
- **Upload API** — authenticated REST endpoint for pre-parsed combat log data, API token management at Settings → API (Pro tier), Sanctum-based auth with rate limits
- **Combat log parser** — server-side parser for raw WoW log files including COMBATANT_INFO gear/aura extraction, WCL-compatible output format
- **Report page redesign** — service result cards with score bar navigation, deep-dive views, redesigned report lists with tier pills and sparklines, sticky header with section scores, keyboard navigation, compare bar, progressive results during analysis, density toggle, copy summary, tier gem icons
- **Snapshot retention** — tier-based retention periods with `expires_at` timestamps and report-page retention banner
- **Platform-wide statistics** — public `/stats` page with activity metrics, class rankings, score distribution, and zone stats
- **Character: role-aware scoring** — healers excluded from Performance, tanks excluded from Performance and Buffs, role-filtered trend charts
- **Dashboard: engagement stats** — six per-user metric cards with tier colors and count-up animations, WCL API budget arc gauge
- **Discord integration** — OG meta tags for rich link previews, webhook notifications for analysis-complete, achievements, and changelog events, Discord account linking via OAuth
- **WarcraftLogs account linking** — link/unlink WCL to email-registered accounts in Settings → Linked Accounts, with lockout guard for WCL-primary users
- **Auth UI polish** — password strength meter, show/hide toggle, real-time confirm match indicator, remember me, repositioned OAuth button
- **Notification Center** — per-type opt-in/out toggles at Settings → Notifications, auto-save, system notifications always-on
- **Consumables redesign** — required vs. optional bonus score separation, per-category compliance cards with progress bars, collapsible per-boss sections
- **Expose Armor rework** — zero-tolerance gap detection (100 ms threshold), time-to-first-expose, zero-uptime threshold removed, updated severity labels
- **Feral Faerie Fire tracking** — tracked separately from regular FF; segmented coverage bar with amber Feral FF indicator
- **Ignite partial resist tracking** — contributing crits now include resisted damage amounts with aggregation at instance, boss, and summary levels
- **Self-hosted font** — Instrument Sans served via @fontsource instead of Google Fonts CDN
- **Admin Phase 3** — report bulk controls, archive/unarchive, member timed bans, login history, forced-acknowledgment alerts, user impersonation with audit logging
- **Admin: expansion config editor** — inline-editable sections, diff view, audit log, override indicators, benchmark_min_samples
- **Admin: GDKP system health** — queue history charts, feature flags, alert thresholds, rate limiter config
- **Admin: GDKP user tools** — search with filters, suspicious activity detection, per-user GDKP profiles, platform-wide bans
- **Profile picture upload** — center-cropped 256×256 WebP via PHP GD at Settings → Profile
- **Transactional mail** — SMTP with branded templates, conditional mail for RaidAnalysisCompleted, queued delivery
- **Accessibility (WCAG)** — skip-to-content, aria-describedby/aria-invalid on inputs, aria-live on status transitions, prefers-reduced-motion, ExternalLink component, semantic heading levels
- **App name: WarcraftPulse** — name moved to `VITE_APP_NAME` for self-hosted configurability
- **Email/password auth** — register, login, forgot password, set password for existing WCL accounts, change email with re-verification
- **Player comparison** — side-by-side comparison of multiple players across any combination of reports at `/compare`
- **Battle.net integration** — gear and talent sync after each analysis, character verification, BNet-only characters, Classic Era namespace support
- **German translations** — full UI i18n parity, language selector in Appearance settings, boss name localization
- **Public user profiles** — `/u/{number}` with achievement showcase and configurable privacy
- **Achievement system** — 45 achievements across 8 categories, daily progress sync, profile showcase pinning
- **Performance scoring** — DPS and Healing scored via class-based median comparison; per-player score badges across all analysis cards
- **Player breakdown redesign** — all result components rebuilt with per-player drill-down, accordion patterns, leaderboards, and score badges
- **Onboarding checklist** — guided setup: WCL connect, first report, character claim, profile configuration
- **Fight Timeline service** — chronological fight timeline with pacing stats and Naxxramas wing timing
- **Re-analysis for Premium+** — manual re-run of analysis pipeline with hourly rate limit and tier-based queue priority
- **Personal WCL API Key** — per-user key at Settings → WCL API Key reduces contention on the shared key
- **Email verification onboarding step** — inline verification code step in the onboarding modal for email/password users
- **WCL credential revocation on expired token** — revokes only the WCL connection instead of logging the user out
- **Security hardening** — CSP, X-Frame-Options, rate limits on auth/submit endpoints, secure session defaults, security.txt, Trivy CI scanning
- **Soft-delete accounts** — 30-day purge delay, admin restore, in-app admin notification on deletion
- **Consumables: suboptimal flagging** — separate penalty weighting for suboptimal vs. missing consumables
- **Consumables: temp weapon enchants** — per-boss detection of melee temporary weapon enchant usage
- **Active Time tracking** — active% per player from DamageDone/HealingDone `activeTime` field
- **Engineering Usage tracking** — explosive and gadget usage per player per boss
- **Class Cooldown scoring** — three-dimension scoring (efficiency/timing/effectiveness), Players × Bosses heatmap
- **Trinket Usage tracking** — on-use trinket and racial ability tracking with efficiency scoring
- **Interrupt response rate** — interruptible cast demand tracking and response rate per boss
- **Per-player Damage Taken scoring** — class-median-relative scoring, tanks excluded
- **Expose Armor drop detection** — gap severity tiers, timeline bar, raid-wide drop totals
- **Dispel Tracking service** — per-type (Magic/Poison/Disease/Curse) leaderboard
- **Changelog page** — `/changelog` with admin publishing flow and per-entry notifications
- **Admin log viewer** — PSR-3 log parsing, level/date/text filtering, cursor pagination
- **Notification bell redesign** — border container, colored icon backgrounds, unread glow, empty state
- **Ctrl+J shortcut** — opens Analyze modal from anywhere

### In Active Development

- **TBC Classic analysis services** — porting analysis pipeline to TBC spell IDs, thresholds, and mechanics
- **Season of Discovery coverage** — analysis service parity with Vanilla Classic
- **Expanded service coverage** — additional Execution checks (kiting, soak assignments) and additional Preparation checks (raid composition analysis)

### Near Term

- **Character profiles**: per-character history across all analyzed raids, including DPS/HPS trends, mechanic score, preparation score, and role-aware benchmarks
- **Mobile responsive layout**: the current UI is desktop-first
- **Comparative reports**: a "your guild vs. last week" diff view
- **Public leaderboards**: opt-in raid scoring by server and faction

### Long Term / Expansion Dependent

- **Retail support**: Mythic+ analysis, affixes, dungeon-specific mechanics

## Suggesting a Feature

Open an issue with the `feature-request` label. For new analysis services, include:

- Which expansion it applies to
- Which WarcraftLogs data type captures it (damage events, buff events, etc.)
- What good and bad execution looks like (threshold rationale)

See [CONTRIBUTING.md](../CONTRIBUTING.md) for details.
