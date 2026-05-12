# Notes — Login + Engineer Dashboard mockups

## Components from §5 used (verbatim, HTML-commented)

- **LoginScreen** — `LoginScreen` only (login is a standalone canvas, no AppShell).
- **EngineerDashboard** — `AppShell`, `SideNav`, `TopBar`, `CyclePill`, `PageHeader`, `NudgeBanner` (used twice: top-of-page overdue banner + in-flow "waiting on you" list with the same name per §5), `NextSessionCard`, `LevelBadge`, `StatusPill` (assigned, scheduled, completed, feedback_submitted, auto_paired — every variant from §5/§7 rendered at least once), `AssignmentCard` x3, `EmptyState` (past cycles). `LevelTimeline` is implied via the L2→L3 row but not the full timeline screen — that belongs on `/profile`.

## Decisions

- Inter (body) + Inter Tight (display) — kept restrained per §7 "doesn't feel Visma: multi-accent / decorative". JetBrains Mono only for tabular numerics (cycle countdown, IDs).
- Magenta `#E70641` appears exactly where §6 demands: the Google CTA on login, the page-level primary CTA, the NextSessionCard primary button, and the self-assessment CTA. It also dots the *current* node of the session timeline strip — that's a borderline call; I judged it as a primary-action affordance ("here is where you are") not decoration. If a reviewer disagrees, swap to ink.
- Level palette is rendered as a thin dot + neutral chip body rather than full color-fills, so L4 (amber) and L5 (magenta-adjacent) don't visually compete with the brand CTA. The hero L3 badge is the one place a level color fills a surface.
- Status pills all carry an icon + label per §6 principle 4.
- Density: dashboard is intentionally dense — hero card, level card, two "waiting on you" rows, cycle progress, three AssignmentCards, soft self-assess CTA, and an EmptyState for past cycles. The user asked not to shy from density.
- Sidebar collapses below `md` via `display:none` plus a top-bar hamburger affordance — the drawer itself isn't wired (no JS state), matching the "mockup" remit.

## Unresolved

- No real Visma wordmark asset — used a magenta `V` monogram. Replace with the brand SVG before launch.
- The "Confirm your level" page CTA and the in-card "Re-assess" / "Start self-assessment" CTAs are three calls-to-action for the same intent. §6 says "one primary action per page" — only one is magenta (the top-bar one); the others are neutral or treated as a section-level primary. Worth deciding which one is canonical.
- AssignmentCard layout works at 3-up on xl; at md it goes 2-up which leaves a slight ragged edge on odd counts. Acceptable for a "last 3" view; revisit if the list grows.
