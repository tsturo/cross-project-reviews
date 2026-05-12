# Cross-Project Reviews — UX/UI Architecture

**Status:** Draft v1 — structural foundation for downstream screen design
**Date:** 2026-05-12
**Scope:** Information architecture, screen inventory, flows, forms, component vocabulary, principles

---

## 1. Information architecture

### Sitemap (top-level)

```
/                          → role-aware redirect
/login                     → Google OAuth (Visma domain only)
/dashboard                 → role-aware home (engineer view by default)
/sessions                  → my sessions list (as reviewer + reviewee)
  /sessions/[id]           → session detail (prep, schedule, feedback entry)
  /sessions/[id]/feedback  → feedback form (role-aware)
/profile                   → my AI level, history, self-assessment
/coach                     → coach hub
  /coach/coachees          → coachee roster
  /coach/coachees/[id]     → coachee detail + reviewer picker
  /coach/assignments       → all assignments this cycle
/insights                  → BG manager / MD analytics (gated)
  /insights/completion
  /insights/levels
  /insights/themes         → Phase 3
/admin                     → admin console (gated)
  /admin/cycles
  /admin/cycles/[id]
  /admin/forms             → form versioning, Phase 3
  /admin/imports
  /admin/overrides
/notifications             → in-app audit trail (Phase 2)
```

### Navigation model

**Persistent left sidebar** (collapsible, icon+label). Sidebar items are filtered by role-derived permissions, not separated by role — an engineer who is also a coach sees both "My sessions" and "Coachees" in one sidebar. Top bar carries: workspace title, cycle status pill, user menu (profile / sign out), and a global search (Phase 2).

Rationale: most users wear 1–2 hats; sidebar avoids context switching and keeps the app feeling like one product, not four.

### Post-auth landing

| Role | Lands on |
|---|---|
| Engineer (no coach hat) | `/dashboard` — "Your next session" + "Your AI level" |
| Coach | `/coach/coachees` if any coachee is unassigned this cycle, else `/dashboard` |
| BG Manager / MD | `/insights/completion` |
| Admin | `/admin/cycles` (most recent cycle) |

A user with multiple hats lands on the most action-demanding view (unassigned coachees > pending feedback > dashboard).

---

## 2. Screen inventory

Checklist — each row is a single screen a downstream agent can take.

### MVP (Phase 1)

- [ ] **LoginScreen** — `/login` — all — Google OAuth gate, Visma-only domain copy.
- [ ] **EngineerDashboard** — `/dashboard` — engineer — Personal home: next session, AI level, pending feedback.
- [ ] **MySessionsList** — `/sessions` — engineer — Tabbed list (as reviewer / as reviewee / past).
- [ ] **SessionDetail** — `/sessions/[id]` — engineer — Single assignment view: counterpart, status, schedule, prep notes, feedback CTA.
- [ ] **FeedbackForm** — `/sessions/[id]/feedback` — engineer — Role-aware feedback form (reviewer or reviewee variant).
- [ ] **ProfileScreen** — `/profile` — engineer — AI level, history timeline, self-assessment trigger.
- [ ] **SelfAssessmentForm** — modal off `/profile` — engineer — 60-sec fallback when survey is stale.
- [ ] **CoachCoacheeRoster** — `/coach/coachees` — coach — Table of coachees with level, status, action.
- [ ] **CoacheeDetail** — `/coach/coachees/[id]` — coach — Coachee profile + reviewer suggestion panel + record-pairing action.
- [ ] **AssignmentRecorder** — modal off CoacheeDetail — coach — Confirm pairing already agreed out-of-band.
- [ ] **CoachAssignmentsBoard** — `/coach/assignments` — coach — All this cycle's assignments, filter by status.
- [ ] **InsightsCompletion** — `/insights/completion` — BG/MD — Completion rate by BG/project/cycle.
- [ ] **InsightsLevels** — `/insights/levels` — BG/MD — Level distribution histogram + filters.
- [ ] **AdminCycleList** — `/admin/cycles` — admin — List of cycles with status, create button.
- [ ] **AdminCycleEditor** — `/admin/cycles/[id]` — admin — Cycle metadata + target filter + auto-pair date.
- [ ] **AdminImports** — `/admin/imports` — admin — Trigger sheet sync, view last run, errors.
- [ ] **AdminLevelOverride** — `/admin/overrides` — admin/coach — Set/override an employee's level with reason.
- [ ] **EmptyState / NotFound** — global — all — Shared fallback screens.

### MVP — email surfaces

- [ ] **EmailTemplates** — rendered React-Email — all — Transactional + reminder + digest templates (see spec §10b for trigger matrix).

### Phase 2

- [ ] **SessionScheduler** — embedded in SessionDetail — engineer — Pick 3 freebusy slots; both confirm.
- [ ] **NotificationsAudit** — `/notifications` — admin/coach — Outbound email log.

### Phase 3

- [ ] **InsightsThemes** — `/insights/themes` — BG/MD — LLM-extracted feedback themes.
- [ ] **InsightsLevelShift** — `/insights/levels?view=shift` — BG/MD — Cohort movement +1 level cycle-over-cycle.
- [ ] **AdminFormEditor** — `/admin/forms` — admin — Version & edit feedback forms.
- [ ] **AutoPairPreview** — modal off CoachCoacheeRoster — coach — "Auto-suggest all remaining" preview before commit.

---

## 3. Cross-cutting flows

### Flow A — First-time auth + onboarding
1. `LoginScreen` → Google OAuth (Visma domain gate).
2. Server resolves role from imported data; if level is `unverified`, route through `SelfAssessmentForm` (skippable once).
3. Land on role-appropriate home (see §1).

### Flow B — Coach assigns a reviewer
1. `CoachCoacheeRoster` → click coachee row.
2. `CoacheeDetail` → `ReviewerSuggestionPanel` lists top 5 ranked candidates with reasons.
3. Coach picks one (or searches via `ReviewerSearchDialog`) → confirms in `AssignmentRecorder` (note: pairing already agreed out-of-band).
4. Assignment created; both parties get email; row in roster moves to `Assigned`.

### Flow C — Auto-pair sweep (T-7 days before cycle end)
1. Cron creates assignments for all unassigned coachees using top-1 scoring.
2. **Coach** sees `AutoPairBanner` on `CoachCoacheeRoster` listing auto-paired rows (badged `auto_paired`).
3. **Reviewer** sees new row in `MySessionsList` and an email.
4. **Reviewee** sees same on their dashboard + `EngineerDashboard` "Your next session" card updates.

### Flow D — Reviewee schedules the session
1. From `SessionDetail`, reviewee opens `SessionScheduler` (Phase 2 calendar; MVP shows a plain date picker that records `scheduled_at`).
2. Picks 3 freebusy slots → reviewer confirms one → `Assignment.status = scheduled`.
3. Calendar event created on both calendars (Phase 2); status pill updates everywhere.

### Flow E — Reviewer submits feedback
1. After session, `EngineerDashboard` shows pending-feedback nudge (red badge if > 7 days).
2. Click → `SessionDetail` → `FeedbackForm` (reviewer variant).
3. Submit → assignment moves to `feedback_submitted`; reviewee gets a notification their raw feedback is viewable on `SessionDetail`.

### Flow F — BG manager explores adoption trends
1. Lands on `InsightsCompletion` → sees BG/project completion %.
2. Switches to `InsightsLevels` → histogram, filters by BG/company/project/role.
3. (Phase 3) `InsightsLevelShift` cohort + `InsightsThemes` for qualitative drill-down.

---

## 4. Form inventory

| Form | Filled by | When | Fields (conceptual) | Submit / Cancel |
|---|---|---|---|---|
| **ReviewerFeedbackForm** | Reviewer | ≤ 7 days post-session | 4 weighted sections (Architecture & patterns, Clean code, Testing/observability/security, AI workflow) each with Likert + text; wrap-up (one habit, one thing to steal). See spec §7. | Submit closes assignment; Cancel saves draft locally. |
| **RevieweeFeedbackForm** | Reviewee | ≤ 7 days post-session | Reviewer stage actually higher? (Likert), top thing learned (text), what you'll try in 2 weeks (text), session quality (Likert), coverage check (multi-select of topics covered), program feedback (optional). See spec §7. | Submit closes their side; Cancel saves draft. |
| **AssignmentRecorder** | Coach | After agreeing pairing out-of-band | Reviewee (prefilled), reviewer (picked or searched), note (optional), confirm-out-of-band checkbox | Submit creates assignment; Cancel returns to coachee detail. |
| **AdminCycleForm** | Admin | Once per cycle | Name, start date, end date, auto-pair date, target filter (last-review-before date, BG/company scope) | Submit creates/updates cycle; Cancel discards. |
| **SelfAssessmentForm** | Engineer | When level is missing / stale (> 6 months) | 5-question version of the survey mapped to L1–L5 + confidence | Submit writes `ai_levels` row (`source = survey`); Cancel re-prompts next login. |
| **LevelOverrideForm** | Coach / Admin | Ad-hoc | Employee (prefilled if from row), new level (1–5), reason (required text) | Submit writes `ai_levels` row (`source = override`); Cancel closes. |
| **ImportTriggerForm** | Admin | Ad-hoc | Sheet (employees / levels), dry-run toggle | Submit runs server action, shows result toast; Cancel closes. |

---

## 5. Component vocabulary

Shared design glossary. Downstream agents must reuse these names verbatim.

1. **AppShell** — sidebar + topbar layout wrapper.
2. **SideNav** — role-filtered persistent navigation.
3. **TopBar** — workspace title, cycle pill, user menu.
4. **CyclePill** — shows active cycle name + days remaining.
5. **PageHeader** — page title + one-line page intent + primary action slot.
6. **LevelBadge** — color-coded L1–L5 chip; includes `unverified` variant.
7. **StatusPill** — assignment status (`assigned`, `scheduled`, `completed`, `feedback_submitted`, `auto_paired`).
8. **AssignmentCard** — compact assignment summary used on dashboards.
9. **NextSessionCard** — hero card on EngineerDashboard.
10. **CoacheeRow** — row in coachee roster with inline expand for suggestions.
11. **ReviewerSuggestionPanel** — top-5 ranked candidates with reason chips.
12. **ReviewerSearchDialog** — fallback search for manual override.
13. **AssignmentRecorderDialog** — modal wrapping AssignmentRecorder form.
14. **FeedbackFormShell** — shared scaffold for both feedback form variants.
15. **LikertField** — 1–5 radio with labels.
16. **PromptingHabitField** — paired strength + growth text inputs.
17. **LevelTimeline** — vertical timeline of an employee's level history.
18. **MetricTile** — single KPI tile for insights dashboards.
19. **DistributionChart** — histogram component for level distribution.
20. **CohortShiftChart** — Phase 3, level-shift over cycles.
21. **EmptyState** — illustration + headline + action; reused everywhere.
22. **DataTable** — generic sortable/filterable table primitive (shadcn DataTable).
23. **FilterBar** — BG/company/project/role filter group for insights.
24. **NudgeBanner** — top-of-page banner for "set your level", "auto-pair tomorrow", etc.
25. **ToastFeedback** — global toast for success/error confirmations.

---

## 6. Cross-cutting UX principles

1. **Every screen opens with a one-line page intent** under the title — describes who it's for and what action it expects.
2. **Level badges are always color-coded with the same scheme L1→L5** (cool→warm gradient); never reuse those colors for status.
3. **Visma magenta `#E70641` is reserved for primary actions only** — never for status, never for decoration. One primary action per page.
4. **Status is communicated by `StatusPill`, not by color alone** — every pill has an icon + label for accessibility.
5. **Empty states are designed, not blank** — every list, table, and dashboard has an `EmptyState` with a next action.
6. **No in-app approval flows** — coach/reviewer agreement happens out-of-band; the app only records. Forms must reflect this in copy ("Record the pairing you agreed on").
7. **Anonymity boundary is explicit** — feedback shown to BG/MD is aggregated; raw feedback only appears on the reviewee's own `SessionDetail`. Every screen that shows feedback labels its audience.
8. **Stale data is flagged, not hidden** — "level unverified", "survey > 6 months old", "import last run X days ago" appear as `NudgeBanner`s, never silent.
9. **Role-mixed users get one sidebar, not a switcher** — engineers who are coaches see both areas in the same nav.
10. **Forms autosave drafts locally** — feedback forms must never lose input on accidental navigation.

---

## 7. Visma brand quick-reference (provisional)

**Verify against the official Visma brand kit before launch.**

- **Primary:** Visma magenta-red `#E70641`. Use for primary CTA, focus rings, brand accents only.
- **Neutrals:** near-white surface `#FAFAFA`, card `#FFFFFF`, charcoal text `#1A1A1A`, muted `#6B7280`, border `#E5E7EB`.
- **Level palette (L1→L5):** slate `#64748B` → teal `#0EA5E9` → green `#10B981` → amber `#F59E0B` → magenta-adjacent `#BE185D`. (Magenta itself stays reserved.)
- **Status palette:** neutral `#6B7280` (assigned), blue `#2563EB` (scheduled), green `#16A34A` (completed), violet `#7C3AED` (feedback_submitted), amber `#D97706` (auto_paired).
- **Type scale:** Inter (or Visma's brand sans if specified). Sizes: 12 / 14 / 16 / 20 / 24 / 32. Line height 1.5 body, 1.25 headings.
- **Spacing scale:** 4 / 8 / 12 / 16 / 24 / 32 / 48 (Tailwind defaults).
- **Radius:** `rounded-lg` (8px) default; `rounded-full` for pills and badges.
- **Feels Visma:** clean Nordic spacing, generous whitespace, single accent color, no gradients on surfaces, restrained iconography (lucide).
- **Doesn't feel Visma:** dark mode-first, glassmorphism, multi-accent rainbows, heavy shadows, decorative illustrations on every page.
