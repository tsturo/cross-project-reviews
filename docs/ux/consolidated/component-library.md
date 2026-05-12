# Component library — consolidated

**Status:** Canonical inventory of every UI component appearing in the swarm. Each entry names the canonical implementation, the variants found, anatomy, states, and props. Future components must align to these shapes.

Format per entry:
- **Used in** — bundles that rendered it
- **Variants found** — significant drift
- **Canonical** — which bundle's version wins, and why
- **Anatomy / props / states** — the contract

---

## §5 components (named in the overview)

### 1. AppShell

- **Used in:** 01, 02, 04, 05, 06, 07, 08, 09, 10
- **Variants:** All used a two-column CSS grid `[260px_1fr]` with a sticky top bar. Bundle 04 reduced sidebar to 240px. Bundle 09 collapsed it under `md`.
- **Canonical:** Bundle 01's `min-h-screen md:grid md:grid-cols-[260px_1fr]` with `bg-white` sidebar, `bg-[var(--surface)]` main column. Sidebar width fixed at **260px**.
- **Anatomy:** `<aside class="sidebar">` + `<div class="main">` containing `<header class="topbar">` + `<main>`. Main column has `max-w-[1240px] mx-auto px-5 md:px-8`.
- **States:** Mobile (<md) collapses sidebar to hidden + hamburger affordance in topbar.

### 2. SideNav

- **Used in:** All authenticated bundles.
- **Variants:** Active item style drifted — bundle 01 used black-fill (`bg-[#111111] text-white`), bundles 06/07/08 used soft gray-fill (`bg-[#F3F4F6]` or `bg-[#F4F4F5]`), bundle 04 used a colored leading dot.
- **Canonical:** **Soft gray-fill** (`bg-[#F4F5F7]` / `--border-2`) with `color: var(--ink)` and `font-weight: 600`. Bundle 01's black-fill is too loud — reserved for hard-selected admin states only. Active item carries a 2px magenta leading dot only on workspace nav for the current page (bundle 08 pattern).
- **Anatomy:** Brand header (magenta V monogram + product name) → grouped nav (`Workspace` / `Coach` / `Insights` / `Admin`) with uppercase mono section labels → footer (Help, user chip).
- **Props:** `groups: { label, items: [{ icon, label, href, badge? }] }`. Active item is single-source (current path), not prop.
- **States:** default · hover · active · with-badge (e.g. "2 todo" amber pill).

### 3. TopBar

- **Used in:** All authenticated bundles.
- **Variants:** Search bar inclusion drifted — bundles 01/06/07 included `⌘K` search; 04/09/10 omitted it.
- **Canonical:** Bundle 01's TopBar — breadcrumb left, `⌘K` search center, CyclePill + bell + avatar right. Search is **always present** even if non-functional in MVP (it's structural).
- **Anatomy:** `<header class="h-14 px-5 md:px-8 flex items-center gap-4">`. Sticky, white background, 1px bottom border.

### 4. CyclePill

- **Used in:** 01, 04, 07, 09, 10
- **Variants:** Bundle 01 used a neutral chip with green dot + mono countdown. Bundle 09 used `#FFF1F8` (pale pink) + `#9D0356` text — magenta-tinted, breaking discipline. Bundle 04 used ink-on-paper.
- **Canonical:** **Bundle 01** — `pill bg-[#F3F4F6] text-[var(--ink)] border border-[var(--border)]` with a green `circle-dot` lucide icon, cycle name, separator dot, and `font-mono` countdown.
- **Anatomy:** `[status-dot] [cycle-name] · [Nd left]`.
- **States:** active (green dot) · closing-soon (amber dot, <14d left) · closed (gray dot).

### 5. PageHeader

- **Used in:** All bundles.
- **Variants:** Eyebrow label drifted — sometimes uppercase mono, sometimes uppercase sans, sometimes missing.
- **Canonical:** Three-line block per §6.1: **uppercase eyebrow** (12px, `letter-spacing: 0.18em`, `var(--muted)`) → **display title** (30–34px Inter Tight 600) → **page intent line** (14.5px `var(--muted)`, max-w-2xl). Right-aligned action slot for primary CTA + secondary buttons.
- **Props:** `{ eyebrow, title, intent, primaryAction?, secondaryAction? }`.

### 6. LevelBadge

- **Used in:** All bundles.
- **Variants:** Three distinct treatments:
  1. **Chip-with-dot** (bundle 01): neutral `#F3F4F6` body + colored 8px leading dot. Used everywhere small.
  2. **Solid color fill** (bundles 03, 04, 07): the full chip is the level color with white text. Used on hero displays.
  3. **Mono code** (bundle 07): `font-mono` `L4` glyph on a solid level-color background, 4px radius — used in dense tables.
- **Canonical:** Both forms are kept, but with a strict rule: **solid fill only on the hero/glyph context** (large numeral on the LevelBadge card, modal level-diff visualization). Everywhere else — list rows, assignment cards, suggestion chips — use **chip-with-dot**. This prevents L4 amber and L5 magenta from competing with `--brand-primary`.
- **Anatomy (chip-with-dot):** `.lvl { display:inline-flex; gap:6px; padding:3px 8px 3px 5px; border-radius:9999px; background:#F3F4F6; color:var(--ink); font-size:11px; font-weight:600; } .lvl-dot { width:8px; height:8px; border-radius:9999px; box-shadow:0 0 0 2px #fff; }`
- **Anatomy (hero):** `h-14 w-14 rounded-xl grid place-items-center font-display text-2xl font-bold text-white` filled with `var(--l3)`.
- **States:** L1 / L2 / L3 / L4 / L5 / **unverified** (gray, dashed border, lucide `help-circle` icon).

### 7. StatusPill

- **Used in:** All bundles.
- **Variants:** Background opacity drifted — bundle 01 used `rgba(…,0.08–0.10)`, bundle 07 used flat solids with white text on certain pills.
- **Canonical:** **Tinted-background variant** from bundle 01: `background: rgba(<status-rgb>, 0.08)`, `color: var(--status-*)`, with a lucide icon + label, 11px font, 3px×8px padding, pill radius.
- **Icons (canonical mapping):**
  - `assigned` → `user-plus`
  - `scheduled` → `calendar-check-2`
  - `completed` → `check-circle-2`
  - `feedback_submitted` → `message-square-check`
  - `auto_paired` (flag, not status) → `sparkles`
- **Rule:** Status and auto-pair are **separate pills** rendered side by side (bundle 07 nailed this). Never combine into one chip.

### 8. AssignmentCard

- **Used in:** 01, 02, 07
- **Variants:** Bundle 01 was vertical card with avatar + status pill + quoted feedback + footer date/CTA. Bundle 07 used table rows (rejected for card use case).
- **Canonical:** Bundle 01's card — `card p-5 flex flex-col gap-4`. Header (avatar + name + project, level chip right), status pills row, body (one of: feedback quote / overdue nudge / pairing reason), footer (date + open link).
- **Props:** `{ counterpart: {name, initials, project}, level, role: 'reviewer'|'reviewee', status, autoPaired?, body, date, href }`.

### 9. NextSessionCard

- **Used in:** 01
- **Variants:** Only one implementation.
- **Canonical:** Bundle 01's `lg:col-span-2 card`. Two-region layout: data block (you-are-the-reviewee, counterpart name as Inter Tight H2, project/stack/level metadata, when/where/focus grid) + 180px counterpart avatar block with vertical border separator. Below: action bar (primary "Open prep notes" + secondary calendar buttons). Bottom strip: mini timeline (Recorded / Scheduled / Session / Feedback due) with magenta dot on current node.
- **Refinement:** Drop the hero-texture radial wash; flat white card.

### 10. CoacheeRow

- **Used in:** 05 (canonical), 07 references it
- **Canonical:** Bundle 05's row — 56px height, hairline separators (no zebra), 16px cell padding. Six columns: avatar+name, level chip, status pill + context line, last-review date, load (mono), action button. Click row → inline-expand `ReviewerSuggestionPanel` with `colspan=6` indented 60px to align with avatar column.
- **States:** default · hover (`bg: #FAFAFA`) · expanded (panel shown below) · urgent (left rail accent stripe, used in auto-pair warning view).

### 11. ReviewerSuggestionPanel

- **Used in:** 05, 06
- **Variants:** Bundle 06 used a numbered ranked list (rows with rank + name + chips). Bundle 05 used the inline expand panel.
- **Canonical:** **Inline expand panel** containing a **ranked list of 5** — combine both: bundle 05's expand structure + bundle 06's row-with-reason-chips format. Each row: rank number (`#1`–`#5` in mono), avatar+name, level chip, project/stack/company, horizontal strip of 4–5 reason chips, "Pick" button.
- **Reason chips:** typed by lucide glyph: `arrow-up` (level diff), `code-2` (stack), `building` (company), `gauge` (load), `target` (skill fit). Amber-tinted for the one warning case (`Overqualified`, `Load 2/2 · near cap`).

### 12. ReviewerSearchDialog

- **Used in:** 06 (inlined as a section, not a dialog)
- **Canonical:** Render as a **modal dialog** in production — bundle 06's inlining is a mockup convenience. Use the same row format as ReviewerSuggestionPanel. Filter bar (level, company, project, stack) sticky at top.

### 13. AssignmentRecorderDialog

- **Used in:** 06
- **Canonical:** Bundle 06's modal. Key copy load-bearing per §6.6: "Record the pairing you've agreed on" + helper line + **required checkbox** "I confirm we agreed this pairing in Slack / email / in person". Submit disabled until checkbox is ticked. Level diff visualization: reviewee badge → arrow → reviewer badge with `+1 level · sweet spot` mono caption (reuses the timeline grammar).
- **Anatomy:** header (title + audit pill) → reviewee summary → reviewer picker (or prefilled) → level diff strip → optional note textarea → confirm checkbox → footer (Cancel · Record pairing).

### 14. FeedbackFormShell

- **Used in:** 03
- **Variants:** Single implementation, two pages (reviewer + reviewee).
- **Canonical:** Bundle 03's shell — sticky session header with autosave indicator (pulse dot + "Draft saved · 12s ago"), two-column section pattern (numbered marker left, question right), audience footer above Submit. The serif title treatment is **rejected**; use Inter Tight for question prompts at 18–20px. Numbered markers and progress strip stay.
- **Section markers:** `01 / 08` mono labels in a left rail.
- **Progress strip:** 8-cell row of squares (done / current / empty). Sits beside the count.
- **Anatomy:** AppShell → sticky session header (counterpart, status, autosave) → numbered sections → audience footer → submit bar.

### 15. LikertField

- **Used in:** 03
- **Canonical:** Bundle 03's "physical scale" — horizontal black rail across the field, 5 detents, Inter Tight numerals stacked over uppercase 11px labels (`Strongly disagree` … `Strongly agree`). Click anywhere on the rail; current value rendered with a magenta-bordered indicator (this is one of the rare magenta uses; allowed because it's the interaction's primary affordance).
- **Props:** `{ name, value, labels: [string,string,string,string,string] }`.

### 16. PromptingHabitField

- **Used in:** 03
- **Canonical:** Paired textareas side by side in a 2-col grid (`md:grid-cols-2`), one labeled "Strength" with `thumbs-up` lucide icon, one labeled "Growth area" with `sprout` icon. Same shell, different head. Char counter `0 / 500` in the bottom-right of each.
- **Note:** Bundle 03 used a serif label — switch to Inter Tight 600.

### 17. LevelTimeline

- **Used in:** 04
- **Canonical:** Bundle 04's vertical rail — `tl-rail` 1px ink line with 11px `tl-node` markers (hollow / filled / current magenta). Each entry: date + cycle code (mono), level glyph, one-line label, prose context, source pill (`Self-assessment` / `Cross-review` / `Survey` / `Override`). Most recent first.
- **Props:** `{ entries: [{ date, cycle, level, label, context, source }] }`.

### 18. MetricTile

- **Used in:** 08, 09
- **Canonical:** Bundle 08's tile — card with eyebrow label (12px uppercase mono `--muted`), giant Inter Tight numeral (32–48px, `font-weight: 600`), one-line description below, optional sparkline or delta chip. Padding `p-6`.
- **Props:** `{ label, value, format: 'number'|'percent', delta?: { value, direction }, spark?: number[] }`.

### 19. DistributionChart

- **Used in:** 08
- **Canonical:** Bundle 08's hand-authored SVG histogram. Five bars (L1–L5) in level colors with **dashed-outline ghost bars** behind for previous cycle. Y-axis hidden, value labels on top of each bar. Width 100% of container; fixed viewBox.
- **Anti-pattern:** No chart library in MVP. SVG only.

### 20. CohortShiftChart

- **Used in:** Not rendered in any bundle (Phase 3).
- **Status:** **TODO.** §5 lists it; specify as a Sankey-style flow from L<n>→L<n+1> using the same level palette.

### 21. EmptyState

- **Used in:** 01, 02, 07
- **Canonical:** Bundle 07's `assignments-empty.html` — card with dashed-bordered icon disc, single ink headline, muted helper line, primary CTA. Centered.
- **Anatomy:** `card p-8 grid place-items-center text-center` → dashed disc with lucide icon (e.g. `archive`, `inbox`, `users`) → 14px ink medium → 12.5px muted helper → action button.

### 22. DataTable

- **Used in:** 05, 07, 09
- **Variants:** Row hover color drifted (`#FAFAFA` vs `#F7F4ED` vs `#FCFCFC`).
- **Canonical:** Bundle 07's table — `tbl-row` with `border-top: 1px solid var(--border)`, hover `#FAFAFA`, 56–60px height, 16px cell padding, no zebra. Header row: 11.5px uppercase mono `--muted`. Sticky header inside scroll container.
- **Density:** Bundle 05's pattern of "one cell, two facts" (status pill + context line below) is the canonical way to fit dense data — adopt as a column-rendering convention.

### 23. FilterBar

- **Used in:** 05, 07, 08, 09
- **Variants:** Filter button active state drifted — bundle 07 used `bg: #111827, color: white`, bundle 08 used `bg: white, border: 2px solid ink`.
- **Canonical:** Bundle 07's segmented filter — `filter-btn` 32px height, 8px radius, border `--border`, active state `bg: #111827 color: white`. Optional count badge inside. Sticky at top of scroll container.
- **Props:** `{ groups: [{ label, options, value, onChange }], reset: () => void }`.

### 24. NudgeBanner

- **Used in:** 01, 05
- **Variants:** Two flavors: standard amber (bundle 01) and dark-inverted (bundle 05's auto-pair warning with serif countdown).
- **Canonical:** **Two variants kept, both named:**
  - **`NudgeBanner` (standard):** `border-[#FDE68A] bg-[#FFFBEB]` amber, `alert-triangle` lucide icon, semibold lead + body, right-side action link + dismiss X. Used for feedback overdue, stale level, import warnings.
  - **`NudgeBanner` (urgent):** dark inverted, `bg-[#1A1A1A] color-white`, magenta accent glow ring, large mono countdown (e.g. `23:47 h·m`), prominent action button. Used for auto-pair imminent and feedback >5 days overdue.

### 25. ToastFeedback

- **Used in:** None of the bundles rendered a toast.
- **Status:** **TODO.** Specify as a 320px right-bottom anchored card with lucide icon (`check-circle-2` / `alert-circle` / `info`), title + body, 6-second auto-dismiss. Use the four generic `--ok` / `--warn` / `--err` / `--info` color tokens.

---

## Components that emerged in mockups but weren't in §5

### A. AutoPairWarningBanner (extends NudgeBanner urgent)

- **Used in:** 05 (`roster-autopair-warning.html`)
- **Why add:** It's distinct enough from a normal NudgeBanner to deserve its own name — live serif countdown, magenta accent ring, re-sorts the table beneath it, has a bulk "Assign them now" action.
- **Add to §5 as #26.**

### B. AutoPairBanner (post-sweep)

- **Used in:** Implied by flow §C in overview but not rendered.
- **Status:** **TODO** — must show "N assignments auto-paired by the system overnight; review or override".

### C. ReasonChip

- **Used in:** 05, 06
- **Canonical:** Small inline chip with lucide glyph + 11px label. Color-coded by type: neutral (stack/company/load), green (level fit, skill fit), amber (warning).
- **Add to §5 as #27.**

### D. LevelDiffStrip

- **Used in:** 06 (modal), implicit in 04 (timeline)
- **Canonical:** Reviewee badge → dotted rule with arrow → reviewer badge, with `+N level · <label>` mono caption beneath. Same grammar in both the AssignmentRecorderDialog and the LevelTimeline.
- **Add to §5 as #28.**

### E. AuditPill

- **Used in:** 10 (level-override)
- **Canonical:** Small pill `bg: #F3F4F6` with `shield-check` lucide icon + "Audit-logged" label. Indicates the action will be permanently recorded.
- **Add to §5 as #29.**

### F. RunHistoryRow

- **Used in:** 10 (`imports.html`, `imports-error.html`)
- **Canonical:** Row in the imports history table: timestamp (mono), status pill (`success` / `partial` / `dry-run` / `failed`), counts (`+12 / ~3 / -0` mono), trigger source, expand → field-level errors.
- **Add to §5 as #30.**

### G. FilterBuilder (vs FilterBar)

- **Used in:** 09 (cycle editor target filter)
- **Canonical:** Sentence-style rule builder ("last cross-project review was before 2025-08-01 AND BG is any of … excluding role is Engineering Manager"), each clause a pill. Distinct from `FilterBar` which is a top-of-page slicer.
- **Add to §5 as #31.**

### H. CycleStatusPill

- **Used in:** 09
- **Canonical:** Variant of StatusPill for cycle states: `Planned` (blue), `Active` (green), `Closing` (amber, <14d left), `Closed` (gray).
- **Add to §5 as #32.**

### I. EmailContainer (transactional email shell)

- **Used in:** 11 (all 7 emails)
- **Canonical:** 600px table, white card on `--surface-warm` canvas, IBM Plex Sans body + Fraunces hero. **Email is its own design system** — Inter doesn't render reliably in Outlook 2016, so the email canvas is the **one place** Fraunces + IBM Plex Sans is the right call. Documented as a separate sub-system below.
- **Add to §5 as #33** (`EmailContainer`) plus #34 (`EmailHeroBlock`), #35 (`EmailCyclePill`), #36 (`EmailFooter`).

### J. AdminToggleSwitch

- **Used in:** 10
- **Canonical:** 32×18px pill toggle. Off state `bg: #E5E7EB`. On state `bg: #111827` (not magenta — magenta is reserved). 14px circular thumb with `0 1px 2px rgba(0,0,0,0.2)` shadow.
- **Add to §5 as #37.**

---

## §5 components no mockup implemented

- **CohortShiftChart** (§5 #20) — Phase 3. **TODO.**
- **ToastFeedback** (§5 #25) — implied everywhere, never rendered. **TODO.**
- **ReviewerSearchDialog** (§5 #12) — bundle 06 inlined it as a section. **TODO**: render as modal.
- **InsightsThemes**, **InsightsLevelShift**, **AdminFormEditor**, **AutoPairPreview** (Phase 3 screens) — out of scope for Stage 2.
- **SessionScheduler** (Phase 2 freebusy widget) — only stub mention in bundle 02. **TODO.**

---

## Sub-system: Email components (bundle 11 canonical)

Email rendering targets Outlook 2016+, Gmail web, Apple Mail, and Outlook for iOS. The in-app design system does **not** apply; emails get their own canonical subset:

- **EmailContainer:** 600px table-based, `bg-card #FFFFFF`, `border: 1px solid #E8E2D7`, sits on `#F4F1EC` canvas.
- **Type:** Fraunces 400 for headlines (44px hero, 22px section), IBM Plex Sans 400/500/600 for body (15px), IBM Plex Mono 500 for cycle pill labels and metadata (10–11px uppercase, `letter-spacing: 0.14–0.18em`).
- **EmailCyclePill:** inline-block, `border: 1px solid #E10078`, `color: #E10078`, no fill, mono uppercase content.
- **CTA button:** `bg: #E10078 color: white`, `padding: 14px 24px`, no radius (square in email for client compat), Inter/Plex Sans fallback stack.
- **EmailFooter:** mono 10–11px, opt-out link present per category, "Cannot be opted out" plain copy for transactional.
- **No SVG, no flex, no grid, no background-image, no JS.** Tables only. Inline styles only.
