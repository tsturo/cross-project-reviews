# Cross-Project Reviews — v1 Simplification Plan

**Status:** Approved for execution — all tiers
**Date:** 2026-05-19
**Audience:** Implementation agent

## Why this exists

Three specialist reviewers (UX flow, UI visual, management/HR ops) audited the v1 design and converged on a set of cuts. This document captures every approved edit, organized in tiers by risk/scope. The goal is **simplification, not addition** — reduce scope, remove appraisal-leaning surfaces, fix token discipline, collapse redundant role surfaces, fix spec contradictions.

## Hard constraints — read before touching anything

- **Do not add new features.** This is a simplification pass. If a task is ambiguous, default to "cut", not "expand."
- **Do not reintroduce gamification ideas** explored in earlier sessions (Maker's Marks, Find Cards, Atlas, The Annual, Workshop, Studio, etc.) — those were already reverted at the user's request.
- **Preserve the editorial aesthetic.** Fraunces (display), Inter (UI), JetBrains Mono (data). Paper `#F6F2EA`, ink `#131211`, muted `#6B6258`, hairline `#E4DED2`. Emails use `#F4F1EC` by design.
- **Magenta `#E70641` is for primary CTA only.** No decorative uses, no hover states, no plus-marks, no avatar backgrounds.
- **`git add` discipline:** stage files by name, never `git add .` or `git add -A`.
- **Commit per tier:** one tier = one commit minimum. Each commit message names the tier (e.g., `v1 simplification — Tier 1: form, self-assessment, spec hygiene`).
- **Stay on the working branch.** Do not merge to master without explicit user confirmation.
- The mockups are **hand-tuned, not regeneratable from a template.** Make surgical edits; do not rewrite files from scratch.
- **No build/lint/test toolchain exists.** Verify visually by opening the HTML files in a browser (`python3 -m http.server 8765` from repo root → http://localhost:8765/docs/).
- **GitHub Pages** rebuilds on push to master only. Don't push your branch to master until the user reviews.

## Project context (skim once)

- **Audience:** ~200 senior engineers at Visma Tech Lithuania (NOT 660 — spec is stale on this; one of the tasks below fixes it).
- **Cadence:** 2 cross-project peer-review sessions per engineer per year.
- **Each session:** 1–1.5h human conversation; more-AI-mature reviewer pairs with less-mature reviewee.
- **Four equally-weighted dimensions:** architecture & patterns · clean code · testing/observability/security · AI workflow.
- **AI maturity levels are read-only in the app** — set in HR check-ins outside.
- **Pairing approval is out-of-band** (Slack, hallway). The app *records* the agreed pairing; no in-app accept/decline.
- **Email is the only system channel in MVP.** No Slack bot.
- **This is developmental, not appraisal.** Explicitly NOT performance evaluation.

## Files you'll touch (paths from repo root)

- Spec: `docs/superpowers/specs/2026-05-12-cross-project-reviews-design.md`
- UX overview: `docs/ux/01-overview.md`
- Mockups under `docs/ux/screens/NN-name/` (11 bundles, 31 HTML files)
- Deck: `docs/index.html` (embeds mockups via iframes — if you delete a mockup, also remove its slide)
- CLAUDE.md at repo root (for design tokens reference)

---

# Tier 1 — High-confidence cuts

Estimated effort: ~2 hours. Low risk, high impact. Commit each task individually or bundle into one Tier-1 commit.

## A. Reviewer form: 15 → 6 questions

**File:** `docs/ux/screens/03-feedback-forms/feedback-reviewer.html`

**Keep:**
- Q1 Architecture strength (free text)
- Q2 Architecture growth (free text)
- Q12 Prompting & context-setting (paired strength + growth free text)
- Q13 Verification habits (radio: blind / spot-check / systematic)
- Q14 One concrete habit they should adopt
- Q15 One thing they're doing well that you'll steal

**Cut entirely:**
- Q3 Overall architectural awareness (Likert)
- Q5 Testability/coverage (Likert + comment)
- Q6 Code organization & naming (Likert + comment)
- Q8 Telemetry/observability (Likert)
- Q9 Security/threat-model awareness (Likert + comment)
- Q10 Observed AI maturity 9-cell grid picker (this is the highest appraisal-risk question in the form — drop entirely; if the reviewer wants to note a level-gap concern, the wrap-up text Q14/Q15 captures it)
- Q11 Tool stack multi-select (violates spec §1 non-goal "Tracking individual prompts or AI tool usage telemetry")

**Replace Q4 (SOLID/DRY/KISS Likert + paired examples):** with a single text field: *"One example of engineering hygiene done well, one example of where it could be tighter."* Drop the Likert.

**Update the section structure:**
- Section 1 — Architecture & design patterns (Q1, Q2)
- Section 2 — Engineering hygiene (the merged Q4 above)
- Section 3 — AI-powered workflow (Q12, Q13)
- Section 4 — Wrap-up (Q14, Q15)

**Form-level changes:**
- Update the "Estimated time remaining" indicator. Real time on the new form is ~6–8 min. If a hardcoded total appears anywhere, set it to **~7 min**.
- Remove the "FORM CONVENTIONS" right-rail panel if it exists (the form should not need an inline legend explaining its own controls).
- Keep the "section not covered" skip affordance — that's a good signal.
- Keep autosave drafts.

**Update spec accordingly:** `docs/superpowers/specs/2026-05-12-cross-project-reviews-design.md` §7 "Reviewer → Reviewee form" — rewrite to reflect the 6-question form. Preserve the structure (sections, equal weight of dimensions in the session even if not in the form).

**Acceptance:**
- The reviewer form loads, contains exactly 6 fields plus the wrap-up Q14/Q15
- No 9-cell grid picker remains
- No tool-stack multi-select remains
- Spec §7 matches what's in the form
- Mockup is visually consistent with prior conventions (Fraunces headings, paper background, hairline borders)

## B. Delete self-assessment screen + dashboard nudge

**Files:**
- Delete: `docs/ux/screens/04-profile/self-assessment.html`
- Edit: `docs/ux/screens/01-auth-and-dashboard/dashboard.html` — remove the "Confirm your level" / "Re-take self-assessment" CTA (it appears as the primary magenta button in the header AND as a card lower on the page; remove ALL surfaces)
- Edit: `docs/ux/screens/04-profile/profile.html` — remove any link to self-assessment (likely a "Re-take self-assessment" button somewhere)
- Edit deck: `docs/index.html` — find any slide that embeds `self-assessment.html` via iframe and remove the slide. Renumber subsequent slides (and the `The tool · NN` eyebrows and any `slide-meta` Role chips). Update slide count if displayed.

**Why:** Levels are read-only in the app per spec §1 non-goals and §4. The self-assessment screen creates a parallel write path the rest of the design pretends doesn't exist.

**If a level is missing:** the engineer sees the existing "unverified" badge (per spec §4) and is excluded from matching until the coach records one. That's the only fallback. No in-app self-assessment.

**Update spec:** Remove any reference to a fallback in-app self-assessment from spec §13 ("60-second in-app self-assessment as a fallback" — drop this sentence). Update UX overview §2 "Flow A" if it mentions self-assessment as a step.

**Acceptance:**
- `self-assessment.html` no longer exists
- Dashboard has no "confirm level" / "self-assessment" CTA in any form
- Profile page has no link to self-assessment
- Deck still loads with all remaining slides renumbered correctly
- Spec/overview have no references to in-app self-assessment

## C. Update spec to 200 engineers, fix Q11 contradiction, fix success metric

**File:** `docs/superpowers/specs/2026-05-12-cross-project-reviews-design.md`

**Change 1 — population numbers:**
- §2 "Users & roles" — change `Every employee (~660)` to `Every employee (~200)`
- Change `Coach | ~100 employees` to `Coach | ~30 employees`
- Search the entire spec for any other `660` or `~100 coaches` references and update consistently.

**Change 2 — UX overview population:**
- `docs/ux/01-overview.md` — search for any references to `660` and update to `200`.

**Change 3 — coachee detail mockup:**
- `docs/ux/screens/06-coachee-detail/coachee-detail.html` around line 322 has a "38 of 660" reference in the suggestion panel headline. Update to a number consistent with 200 (e.g., "38 of 200" or, better, just "38 eligible reviewers").

**Change 4 — fix the §7 Q11 contradiction:**
- §1 "Non-goals" includes "Tracking individual prompts or AI tool usage telemetry."
- §7 Q11 ("Tool stack — what AI tooling did they use? (multi-select)") contradicts this.
- Already handled in task A (Q11 removed from form). Also remove Q11 from spec §7. Confirm no other spec section references tool-stack tracking.

**Change 5 — fix the success metric math:**
- §14 "Success metrics" currently says: `Year-over-year: median AI level +1 across the org.`
- A +1 median per year on a 1–5 scale is mathematically untenable across more than a couple of years.
- Replace with: `Year-over-year: median AI level moves upward, with measurable cohort progression (% of engineers who advanced ≥1 level). Target: 30–40% cohort advance per year in early cycles, normalising as the population matures.`

**Acceptance:**
- No "660" anywhere in the repo
- No "100 coaches" anywhere in the repo
- §7 contains no Q11 / no tool-stack tracking
- §14 success metric is mathematically coherent

## D. Magenta discipline pass

**Files to audit (in order of severity):**
1. `docs/ux/screens/04-profile/profile.html` — the worst offender. Remove decorative magenta from:
   - Sidebar brand dot (if used decoratively beyond the canonical brand mark)
   - `.plus-mark` punctuation on H1/H2 (delete the punctuation entirely or render in ink)
   - Timeline current-node ring (use ink with thicker stroke instead of magenta)
   - Row-hover CTA color (use ink underline or muted hover)
   - L1–L5 scale marker (use the level palette colors, not magenta)
   - Label tracker dot (use muted)
2. `docs/ux/screens/04-profile/self-assessment.html` — DELETED in task B, skip.
3. `docs/ux/screens/01-auth-and-dashboard/dashboard.html` ~L328 — next-session timeline strip's magenta active dot → change to ink.
4. `docs/ux/screens/09-admin-cycles/cycle-list.html` ~L51 — `.cycle-pill` uses `#FFF1F8` bg + `#9D0356` text. Change to neutral chip with optional ink dot.
5. `docs/ux/screens/10-admin-imports/imports.html`, `imports-error.html`, `level-override.html` — any magenta used for "active nav state dot" or "active import" icons → change to ink.
6. **Avatar backgrounds across screens** — `bg-[#FCE7F3]` + `text-[var(--magenta)]` user avatars in: `dashboard.html` L135 & L186, `level-override.html` L157, L234, L481, `imports.html` L131. Change avatars to a neutral palette (e.g., `bg-[#F3F4F6]` with ink text, or use existing identicon colors).

**Rule:** Magenta `#E70641` (also `var(--magenta)` or `#E70641` hex) appears only on **primary CTA buttons**. Audit any remaining occurrence with `grep -rn "E70641\|#E10078\|magenta" docs/ux/` and validate each one is a CTA. If not, change to ink or muted.

**Acceptance:**
- Profile page reads as "editorial paper-and-ink" not "magenta-themed"
- Primary magenta CTAs remain (clearly visible on each screen, one per page where applicable)
- `grep` audit comes back clean: every magenta hex is a CTA

## E. Unify LevelBadge rendering across all screens

**Canonical pattern** (from `docs/ux/screens/01-auth-and-dashboard/dashboard.html` ~L46–52):

```css
.lvl {
  display: inline-flex; align-items: center; gap: 6px;
  padding: 3px 8px 3px 5px; border-radius: 999px;
  background: #F3F4F6; color: var(--ink); font-size: 11px; font-weight: 600;
  letter-spacing: 0.02em;
}
.lvl-dot { width: 8px; height: 8px; border-radius: 999px; box-shadow: 0 0 0 2px #fff; }
```

Rendered as: neutral grey chip body, ink text "L4", small 8px colored dot (using `--l1` through `--l5`).

**Files that drift and need fixing:**
1. `docs/ux/screens/05-coach-roster/roster-normal.html` ~L82–86 — `.level-mark` uses the level color as **both background and text** (tinted). Replace with the `.lvl` chip pattern.
2. `docs/ux/screens/03-feedback-forms/feedback-reviewer.html` ~L63–70 — `.level-chip` uses level color as full background, white text. Replace with `.lvl` pattern.
3. `docs/ux/screens/02-engineer-sessions/sessions-list.html` ~L316 — L5 chip rendered as `background:#BE185D14;color:#9F1239` (magenta-adjacent pink). Replace with `.lvl` pattern.
4. Any other file using level color as background fill — search for `bg-[#FEF3C7]` or similar and audit.

**Acceptance:**
- Every "L1" / "L2" / "L3" / "L4" / "L5" reference in the UI uses the same chip pattern
- Level colors appear only as 8px dots, never as backgrounds or text colors
- Visual diff before/after: levels look like metadata, not status

---

# Tier 2 — Bigger but still scoped

Estimated effort: ~1.5 hours. Higher impact, slightly bigger touch. Commit as a separate Tier-2 commit.

## F. Trim emails 8 → 6

**Files:** `docs/ux/screens/11-emails/`

**Delete entirely:**
- `email-coach-unassigned-t14.html` — overlaps with `email-autopair-imminent.html`; T-7d is enough warning.
- Any "T-1h session reminder" template (Google Calendar handles this). Find via filename or content search.
- Any "feedback overdue T+5d cc-coach" variant. If a single `email-feedback-overdue.html` template handles both T+1d and T+5d via a variable, just remove the T+5d cc-coach behavior, not the file.

**Merge:**
- `email-cycle-opens.html` + `email-pairing-recorded.html` → for most engineers cycle-opens and their first pairing happen within days. Combine into a single `email-cycle-opens-with-pairing.html` (or update `email-cycle-opens.html` to include the pairing details when available). Delete `email-pairing-recorded.html`.

**Resulting set:** 6 templates
- email-cycle-opens-with-pairing
- email-session-24h
- email-feedback-received
- email-feedback-overdue (T+1d only, no cc-coach)
- email-autopair-imminent
- email-cycle-summary-digest

**Update spec §10b "Trigger matrix":** Remove the deleted/merged triggers. Reduce the table to the 6 surviving triggers.

**Update overview** `docs/ux/01-overview.md` if it references the deleted templates.

**Acceptance:**
- Email folder contains exactly 6 templates
- Spec §10b table matches
- Each remaining template renders cleanly (visual smoke test)

## G. Cut the Coach Assignments Board

**Files:**
- Delete: `docs/ux/screens/07-coach-assignments/assignments-board.html`
- Delete: `docs/ux/screens/07-coach-assignments/assignments-empty.html`
- Optional: remove the `07-coach-assignments/` directory entirely if empty after deletion.

**Edit related surfaces:**
- `docs/ux/screens/05-coach-roster/roster-normal.html` — add (if not present) a filter chip set including "Needs attention" / "Unassigned" / "Pending feedback" / "All" so the roster can answer the questions the assignments board used to answer.
- Sidebar (`SideNav` block in each mockup) — remove any "Assignments" link under the "Coach" section. Keep only "Coachees" (which points to the roster).
- Deck `docs/index.html` — remove any slide that embeds an assignments-board mockup. Renumber subsequent slides + eyebrows + role chips.

**Update spec §8.2 "Coach dashboard":** Fold the "needs attention" filter into the coach roster description. Remove any mention of a separate assignments board.

**Acceptance:**
- `07-coach-assignments/` directory empty or removed
- Coach roster has the filter chips needed to surface unassigned + pending-feedback coachees
- Side-nav has no "Assignments" link
- Deck loads with renumbered slides

## H. Drop BG MD insights levels view

**Files:**
- Delete: `docs/ux/screens/08-insights/insights-levels.html`
- **Keep:** `docs/ux/screens/08-insights/insights-completion.html` (completion is operational, not appraisal — it's a "are sessions happening" view, not a "how good are your engineers" view).

**Edit:**
- Sidebar — remove any "Levels" link under "Insights" section. Keep "Completion".
- Deck `docs/index.html` — remove any slide that embeds `insights-levels.html`. Renumber slides.

**Update spec §8.3 "BG Manager / MD dashboard":**
- Remove: "AI-level distribution histogram (filterable by BG, company, project, role)"
- Remove: "Level-shift cohort view: % of engineers who moved +1 level cycle-over-cycle"
- Keep: "Completion rate per BG, project, cycle"
- Keep: "Drill-down to anonymized session feedback themes (top tags from free-text)" — but flag it as Phase 3, not MVP.

**Optional consideration:** if you also want to simplify §2 "Users & roles", you can collapse the BG Manager / MD role to "read-only org view via admin export" instead of a first-class app role. Do NOT do this in Tier 2 — that's a Tier 3 architectural change.

**Acceptance:**
- `insights-levels.html` deleted
- Sidebar "Insights" has only "Completion"
- Spec §8.3 contains only completion metrics
- Deck loads correctly

---

# Tier 3 — Architectural simplification

Estimated effort: ~3–4 hours. This is the biggest change. Treat it as a separate commit (or multiple commits if you want to land it incrementally).

## I. Collapse to one engineer role + one approval surface

**The change:** The 4-role model (engineer / coach / BG manager / admin) is too heavy for 200 people. Simplify to:

- **Engineer** — every employee. Has a "coachees" view if they have any.
- **Admin** — 1–2 people, retains the cycle/import/level-override surfaces.

The coach role is *not deleted*, but it stops being a separate first-class identity. Coaches are engineers who happen to have coachees. Their "coach actions" appear as an additional section in their dashboard, not a separate persona. The BG MD role is dropped from MVP entirely (their data needs are served by an admin-generated CSV once per quarter).

**File changes:**

### I.1 — Sidebar IA simplification

For each mockup with a sidebar (`SideNav`), replace the multi-section nav with:
- **Workspace:** Dashboard · My sessions · Profile
- **Coachees** (shown only if user has coachees; replaces the entire "Coach" section)
- **Admin** (shown only to admins; replaces the entire "Insights" + admin sections for everyone else)

Files that have a sidebar: roughly every mockup except the standalone email templates. Audit by grepping for `<aside` or `class="sidebar"`.

### I.2 — Replace the per-coachee picker with a single "Approve Cycle Pairings" screen

**Create:** A new mockup file at `docs/ux/screens/05-coach-roster/approve-pairings.html` that shows all of a coach's coachees in one table with:
- Coachee name + level + project
- System-proposed reviewer + reviewer's level + project + reason (e.g., "Different stack, rank +1, 0/2 load")
- Override action (opens a chooser with the top-5 candidates)
- A single "Approve all" button at the top + per-row approve checkbox

This screen replaces `roster-normal.html`'s per-coachee click-to-detail flow for the pairing decision. The coach can still open a coachee's detail page for context, but the *decision* happens on one screen.

**Keep:** `roster-normal.html` as a deeper view (the coachee roster with status + level + last review date). But change the flow: the coach lands on `approve-pairings.html` as their primary surface during the pairing window, then can drop into `roster-normal.html` for browsing.

### I.3 — Delete the BG MD role

**Spec changes:**
- `docs/superpowers/specs/2026-05-12-cross-project-reviews-design.md` §2 — remove the "BG Manager / MD" row from the Users & roles table.
- §8.3 — convert from "BG Manager / MD dashboard" to "Admin org export" — a one-paragraph description of an admin-only quarterly CSV.
- Update any access-control discussion in §9 Row-Level Security to remove the BG MD policy tier.

**File deletions:** Already handled in task H (insights-levels deleted). Confirm no other BG-MD-only screens exist.

### I.4 — Coach login lands on approve-pairings during pairing window

Update the auth/landing logic description in `docs/ux/01-overview.md`:
- Engineer with 0 coachees → lands on Dashboard
- Engineer with coachees AND cycle is in the pairing window → lands on `approve-pairings.html`
- Engineer with coachees AND cycle is mid-cycle (sessions running) → lands on Dashboard with a "Coachees" section visible
- Admin → lands on Admin (unchanged)

### I.5 — Update the deck

`docs/index.html` may need slide reordering to reflect the new IA: the "Approve Cycle Pairings" screen becomes the canonical coach demo, replacing whatever currently shows the per-coachee picker flow.

**Acceptance for Tier 3:**
- 3 roles in the spec (engineer / admin), with "coach" as a permission-modifier on engineer
- A new `approve-pairings.html` exists and is the primary coach surface
- BG MD references removed from spec
- Sidebar nav is simpler (3 sections max)
- Deck still flows

---

# Cross-cutting things to address (before launch, not strictly MVP)

These are gaps the HR/management reviewer flagged. They don't require mockup work but should be added to the spec as TODOs before the program runs.

## J. DPO retention policy

**Spec §13:** Currently says "Retention: keep raw feedback 2 years, anonymized aggregates indefinitely. Confirm with Visma DPO."

**Update to:** A concrete policy: raw feedback retained for 1 cycle (6 months) only; anonymized aggregates indefinitely. Engineer can request deletion of their own feedback via a "My data" link in the profile (not designed in MVP, but reserved). On employee offboarding, all raw feedback authored by them is purged within 30 days; anonymous aggregates persist. **Mark the section as "Pending DPO sign-off" — but with a concrete proposal, not a TODO.**

## K. Appraisal firewall copy

**Add a new spec section §13.5 "Privacy notice (engineer-facing)":**

```
This feedback is developmental, not appraisal. Specifically:

- It will NOT be used in performance reviews, salary decisions, or promotion processes.
- It will NOT be visible to your line manager (unless they are your coach), HR, or compensation team.
- It IS visible to: the reviewee, the reviewee's coach, the program admin (for moderation only).
- Raw text is retained for 6 months, then anonymized.
- You can request deletion of your own feedback at any time via the program admin.
```

This copy should be shown to every reviewer before they submit a feedback form. Reserve a spot in the reviewer form mockup (`feedback-reviewer.html`) — a small disclosure block above the Submit button. Do not redesign the form; just add the disclosure.

## L. Missing-coach-data remediation workflow

**Spec §13 "Risks & open questions" — keep the bullet about 16% missing coach data, but add:** On the coach roster, surface a banner *"Level missing for N of your coachees — set in your next 1:1"* with a link to a (Phase-4) inline level-set form. For MVP, the link goes to admin contact instructions.

## M. Off-ramp for "I don't want a session this cycle"

**Spec §5 "Review cycle workflow":** Add an explicit off-ramp. A coachee can decline participation in a cycle by writing to their coach. Coach records the decline in the assignment recorder (a single "decline" status alongside the existing statuses). Common reasons: sickness, parental leave, recent project change, conflict with only eligible reviewer. **Do not design a UI for this; just document the workflow and add a `declined` value to the `Assignment.status` enum.**

---

# Definition of done

Use this as your acceptance checklist. Each box should be tickable when Tier 1+2+3+cross-cutting items are complete.

**Tier 1:**
- [ ] Reviewer form has 6 questions, no 9-cell grid, no tool-stack multi-select
- [ ] Self-assessment screen deleted, dashboard nudge removed, deck updated
- [ ] Spec says 200 engineers / ~30 coaches consistently; "660" appears nowhere
- [ ] Spec §14 success metric is mathematically coherent
- [ ] Profile page has no decorative magenta
- [ ] LevelBadge unified across all screens (neutral chip + colored dot)

**Tier 2:**
- [ ] 6 email templates remain (named correctly)
- [ ] Coach Assignments Board deleted, roster filters expanded
- [ ] insights-levels deleted, sidebar updated
- [ ] Spec §10b, §8.2, §8.3 updated accordingly

**Tier 3:**
- [ ] Sidebar simplified to 3 sections
- [ ] approve-pairings.html exists
- [ ] BG MD role removed from spec §2
- [ ] Landing logic documented in overview

**Cross-cutting:**
- [ ] DPO retention policy is concrete in §13
- [ ] §13.5 Privacy notice exists
- [ ] Reviewer form shows the privacy disclosure above Submit
- [ ] Missing-coach-data banner described in §13
- [ ] Off-ramp documented in §5; `declined` status added to assignment enum

**Final checks:**
- [ ] `grep -rn "660\|magenta" docs/ux/` returns no surprises
- [ ] Deck (`docs/index.html`) loads without broken iframes; slide count + role chips + eyebrows are consistent
- [ ] All commits have descriptive messages
- [ ] Branch is not merged to master — leave it for the user to review

---

# Out of scope for this work

Do NOT do any of these, even if tempting:

- Adding new screens or features
- Reintroducing the "Studio / Workshop / Atelier / Maker's Mark / Find Card / Atlas / The Annual" concepts from earlier sessions (already reverted by the user)
- Performance optimization
- Introducing a build system, linter, or test framework
- Adding new email templates
- Designing the Phase 4 in-app check-in (it's mentioned in the spec but explicitly out of MVP)
- Changing the Visma brand color or core typography
- Mobile-specific redesigns (the mockups are desktop-first by design; verify they don't break catastrophically at narrow widths, but don't redesign)

---

# Notes on commit hygiene

- One commit per tier (minimum). Smaller commits within a tier are fine.
- Commit messages: `v1 simplification — Tier N: <short description>`. Example: `v1 simplification — Tier 1: form, self-assessment, spec hygiene`.
- Stage files by name. Never `git add .` or `git add -A`.
- Do not push to master. Leave the branch for the user to review.
- Do not amend the commit that introduces this plan file — your work goes on top of it.

If you hit a contradiction with the spec or a question you can't resolve from this document, **stop and ask the user**. Default behavior is to cut, not expand.
