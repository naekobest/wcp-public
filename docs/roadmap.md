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

- **Dashboard: engagement stats** — six per-user metric cards (raids, scores, streak, raiding since) with tier colors and count-up animations; WCL API budget arc gauge
- **Discord integration** — OG meta tags for rich link previews; webhook notifications for analysis-complete, achievements, and changelog events; Discord account linking via OAuth
- **WarcraftLogs account linking** — link/unlink WCL to email-registered accounts in Settings → Linked Accounts, with lockout guard for WCL-primary users
- **Auth UI polish** — password strength meter, show/hide toggle, real-time confirm match indicator, remember me, repositioned OAuth button
- **Notification Center** — per-type opt-in/out toggles at Settings → Notifications, auto-save, system notifications always-on
- **Consumables redesign** — required vs. optional bonus score separation, per-category compliance cards with progress bars, collapsible per-boss sections
- **Expose Armor rework** — zero-tolerance gap detection (100 ms threshold), time-to-first-expose, zero-uptime threshold removed, updated severity labels
- **Feral Faerie Fire tracking** — tracked separately from regular FF; segmented coverage bar with amber Feral FF indicator
- **Admin Phase 3** — report bulk controls, archive/unarchive, member timed bans, login history, forced-acknowledgment alerts, user impersonation with audit logging
- **Profile picture upload** — center-cropped 256×256 WebP via PHP GD at Settings → Profile
- **Transactional mail** — SMTP with branded templates, conditional mail for RaidAnalysisCompleted, queued delivery
- **Accessibility (WCAG)** — skip-to-content, aria-describedby/aria-invalid on inputs, aria-live on status transitions, prefers-reduced-motion, ExternalLink component, semantic heading levels

### Near Term

- **Character profiles**: per-character history across all analyzed raids, including DPS/HPS trends, mechanic score, preparation score, and role-aware benchmarks
- **Mobile responsive layout**: the current UI is desktop-first
- **Season of Discovery coverage**: full analysis service parity with Vanilla Classic

### Medium Term

- **GDKP module**: gold distribution management for GDKP raids. Planned scope includes organization management, calendar and sign-ups, officer draft sheets, gold sheet calculation with revision history, and role-based cut assignments. Analysis data feeds automatically into deduction logic (parse percentiles, DPS, healing, debuff uptime). Finalized gold sheets are always public for transparency.
- **Expanded service coverage**: additional Execution checks (kiting, soak assignments) and additional Preparation checks (raid composition analysis)
- **Comparative reports**: a "your guild vs. last week" diff view
- **Public leaderboards**: opt-in raid scoring by server and faction

### Long Term / Expansion Dependent

- **Retail support**: Mythic+ analysis, affixes, dungeon-specific mechanics
- **API access**: for guild management tools and third-party integrations

## Suggesting a Feature

Open an issue with the `feature-request` label. For new analysis services, include:

- Which expansion it applies to
- Which WarcraftLogs data type captures it (damage events, buff events, etc.)
- What good and bad execution looks like (threshold rationale)

See [CONTRIBUTING.md](../CONTRIBUTING.md) for details.
