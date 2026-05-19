# Cross-Project Code Reviews — AI Adoption Edition

**Status:** Draft for review
**Date:** 2026-05-12
**Author:** Tomek Szturo (brainstorm w/ Claude)

## 1. Goal

Bring back the practice of 1–2 cross-project code reviews per engineer per year, refocused on **how engineers work** in 2026 — not on grading individual lines of code.

The session covers, in roughly equal weight:

- **Architecture & design patterns** — event-driven design, CQRS, sagas, message queues, Kafka / pub-sub, Elasticsearch / search strategy, caching, service boundaries, idempotency.
- **Clean code & engineering hygiene** — SOLID, DRY, KISS, testability, naming, refactoring discipline, code organization.
- **Testing, observability, security** — test strategy, telemetry, threat modeling, secrets, dependency hygiene.
- **AI-powered workflow** — how the engineer uses AI (tooling, prompting, verification, orchestration). The AI dimension is one of four, not the only one.

AI maturity matters as a **pairing mechanic** — the reviewer is at a higher rank on the maturity grid, so the reviewee gets exposure to more advanced ways of working. But the *content* of the conversation is broader: an L4 engineer talking to an L2 might spend more time on event sourcing than on prompts.

### Non-goals (explicitly out of scope)

- **Setting AI maturity levels.** Levels are set in the quarterly AI Skill Check-in conversation between coach and coachee, outside this app. This app reads levels, it doesn't write them in the normal flow. (A future phase may host the check-in form itself; not MVP.)
- Replacing day-to-day code review inside teams.
- Performance evaluation — feedback is developmental, not appraisal-grade.
- Tracking individual prompts or AI tool usage telemetry.
- Building an LMS / training platform — this is a peer-exchange tool.

## 2. Users & roles

Two first-class roles plus one permission modifier. Earlier drafts had four (Engineer / Coach / BG MD / Admin); this collapses to two surfaces (Engineer / Admin) because at 200 people the role count was carrying more weight than the actual access differences justified.

| Role | Who | What they do in the app |
|---|---|---|
| **Engineer** | Every employee (~200) | See own upcoming sessions (as reviewer & reviewee), fill feedback forms, view own AI level & history. If they have coachees, additionally see a Coachees view and the cycle pairing-approval surface. |
| **Admin** | 1–2 platform owners | Manage cycles, override pairings, import data, reset levels, generate the quarterly org-completion CSV for BG/MD readers. |

**Coach** is no longer a distinct identity. Coaches are engineers who happen to have coachees on file (resolved at request time from the import). Their "coach surfaces" — Coachees, Approve pairings, Coachee detail — appear inside the same workspace alongside their own sessions, rather than as a separate persona.

**BG Manager / MD** is no longer a first-class role. Their data needs (completion rate per BG / project / cycle) are served by the admin-generated quarterly CSV, not an in-app dashboard. Level distribution and cohort-shift views were dropped from MVP to keep the program developmental rather than appraisal-coded.

Role information is derived from the imported data (`Is Coach?` column, `Department Manager`, etc.), not stored as enums on users — except `admin`, which is set manually.

## 3. Core domain model

```
Employee (id, email, name, company, project, coach_email, role, …)
  ├── current AI level (1–5, source: latest survey or coach override)
  └── history of levels (audit)

ReviewCycle (id, name, starts_at, ends_at, auto_pair_after)
  └── Assignment (cycle_id, reviewee_id, reviewer_id, status, scheduled_at, …)
        └── Feedback (assignment_id, author_id, answers_json, submitted_at)
```

`Assignment.status`: `assigned → scheduled → completed → feedback_submitted`. Auto-paired assignments carry an `auto_paired = true` flag.

Approval between coach and reviewer happens out-of-band (Slack / email / hallway). The coach only records the agreed pairing in the app — no in-app accept/decline flow.

## 4. AI maturity (read-only input from check-ins)

Visma's AI Skill Maturity model is **two-dimensional**:

- **Axis 1 — Level (1–5):** complexity of the agentic setup.
- **Axis 2 — Loop position:** *Human-in-the-loop* (gates each action) vs *Human-on-the-loop* (lets agents run, supervises).

Vehicle metaphors used in Visma's deck:

| Level | Human-in-the-loop | Human-on-the-loop |
|---|---|---|
| L1 | 🚶 Walker (no/minimal AI) | — |
| L2 | 🛴 Scooter (IDE agent, permissioned) | 🏍️ Motorbike (IDE agent, YOLO) |
| L3 | 🚲 Bicycle (CLI agent, permissioned) | 🏎️ Race car (CLI single agent, YOLO) |
| L4 | 🏍️ Motorbike (multi CLI, permissioned) | ✈️ Plane (multi CLI, YOLO) |
| L5 | — | 🚀 Rocket (orchestrator / fleet) |

**Alex's Goal** (the org-wide aspiration): cross from L3 in-loop (bicycle) to L3 on-loop (race car) — the transition from gating to supervising.

**Source of truth:** the AI Skill Check-in conversation between coach and coachee (Q1 / Q4). Levels live in HR-side systems and are imported into this app; this app does **not** set levels in the normal flow.

**Stored as:** `(level: 1..5, loop_position: 'in' | 'on')` plus `source`, `set_at`, `set_by`, optional `reason`. Displayed as a level number + vehicle glyph + loop indicator everywhere a level appears.

**Matching uses a derived rank** that orders the 9 cells (in-L1 < in-L2 < on-L2 < in-L3 < on-L3 < in-L4 < on-L4 < on-L5) so the reviewer is always strictly above the reviewee on that rank.

**Missing data:** if no level on file, display "unverified" badge and exclude from matching until the coach records one from the check-in (manual entry screen — admin/coach only).

## 5. Review cycle workflow

```
[Cycle opens]
   │
   ▼
Coach dashboard lists each coachee + suggested eligible reviewers
   │
   ▼
Coach agrees pairing with reviewer out-of-band (Slack/email)
   │
   ▼
Coach records the pairing in the app
   │
   ▼
Calendar slot proposal (3 options) ──► both confirm ──► scheduled
   │
   │ (if coach inaction at T-7 days before cycle end)
   ▼
System auto-pairs unassigned coachees; notifies coach + both parties by email
   │
   ▼
Session happens (1–1.5h)
   │
   ▼
Both parties fill feedback form (different forms — see §7)
   │
   ▼
Assignment closes; data feeds analytics + next-cycle level recalc
```

A cycle is typically **6 months** (matches the "1–2 reviews per year" rhythm). Admin creates cycles; each cycle has a target set (e.g. "everyone whose last review > 6 months ago").

## 6. Matching rules

**Hard constraints** — every pairing must satisfy all of these. The system filters out anyone who fails. The constraints are what make this *cross*-project, not within-team:

1. `reviewer.rank > reviewee.rank` on the 9-cell maturity grid from §4 (strict; reviewers at the top of the rank — `on-L5` — pair with each other if both end up as reviewees, flagged).
2. **Different `project`.** The single most important rule. Same project = within-project review, not what this program is for.
3. **Different `coach`.** Sharing a coach means sharing the same direct-management context. Cross-coach is the cleanest test of "outside eyes".
4. **No prior pairing in the previous 2 cycles** (~1 year). Avoid repeat exposure that adds little new perspective.
5. **Reviewer load cap** — at most 2 active assignments per cycle.
6. **Both employees are active** in the cycle's target population (not on leave, not pending offboarding).
7. **Same primary language** or both speak the lingua franca (English). Soft-checked via profile; flagged not blocked.

**Soft scoring** (rank among eligible candidates):

| Signal | Weight | Rationale |
|---|---|---|
| Different tech stack | +1 | A .NET engineer learning event sourcing from a Python engineer is the whole point |
| Different BG | +1 | Cross-BG perspective on architecture patterns |
| Reviewer rank ≈ reviewee rank + 1 | +2 | Closer rank is usually more useful than a chasm |
| Reviewer rank ≥ reviewee rank + 3 | −1 | Very large gap can feel patronizing |
| Lower current reviewer load | +1 per slot free | Spread the work |
| `AI Skill Utilization = Skill Fit` | +0.5 | Stable practitioners are better teachers than struggling ones |

**Auto-pair** uses the same hard filter + soft score, picks top-1, and notifies coach + both parties. If no candidate passes the hard filter, the assignment is left unfilled and surfaced to admin with a flag (`unmatched: no eligible reviewer`).

**Validation surface.** Every assignment — whether picked by a coach or auto-paired — runs the hard filter again at the moment of creation. If a constraint fails (e.g. the suggested reviewer got promoted to a new project between suggestion and recording), the form rejects with a specific error: "Cannot pair: same project (Visma.net Expense 755)".

The coach sees the top ~5 ranked candidates with a reason ("Level 4, .NET, different BG, 0/2 load"). They pick; they can also search and override.

**Auto-pair** uses the same scoring, takes top-1, and notifies coach + both parties.

## 7. Feedback forms

Two short forms (one per side), filled within 7 days of the session. Anonymized in aggregate views; visible to reviewee + coach in raw form.

### Reviewer → Reviewee form

Short by design — ~7 minutes. The session itself does the heavy lifting; the form captures take-aways, not appraisal grades. Free-text dominant, no Likerts. The session conversation still covers all four dimensions (architecture, clean code, testing/obs/security, AI workflow); the form measures three of them explicitly — testing/obs/security is folded into the engineering-hygiene prompt.

**Section 1 — Architecture & design patterns**

1. Strength: which architectural decision or pattern did they use well? (free text)
2. Growth area: which architecture / pattern would have served them better? (free text)

**Section 2 — Engineering hygiene**

3. One example of engineering hygiene done well, one example of where it could be tighter. (free text)

**Section 3 — AI-powered workflow**

4. Prompting & context-setting — strength + growth. (paired free text)
5. Verification habits. (radio: blind / spot-check / systematic)

**Section 4 — Wrap-up (required)**

6. One concrete habit (any section) they should adopt from your workflow. (free text)
7. One thing they're doing well that you'll steal. (free text)

Autosave drafts. Sections can be skipped if not observed during the session ("not covered"). Only Q6 and Q7 are required.

### Reviewee → Reviewer form

Short — keep momentum.

1. Was the reviewer's stage genuinely higher in practice across topics that matter to you? (yes / mostly / partially / no)
2. **Top thing learned** — can be architecture, clean code, testing, security, AI workflow, anything.
3. **What you'll try in the next 2 weeks** — concrete commitment.
4. Quality of session (Likert).
5. Coverage check — which topics were actually covered? (multi-select: Architecture · Clean code · Testing · Observability · Security · AI workflow · Other)
6. Optional: feedback for the program.

Forms are **versioned**; admin can edit between cycles without losing history.

## 8. Dashboards

### 8.1 Engineer dashboard (default landing)

- "Your next session" card (role + counterpart + date + prep link).
- "Your AI level" card (current + last change + survey CTA if stale).
- History list of past sessions with feedback received.
- Pending feedback forms (red badge if overdue).

### 8.2 Coach dashboard

A single surface — the coachee roster — answers all of a coach's cycle questions. No separate "assignments board": the same table is filtered to surface what needs doing.

- Coachee table: name, level, last review date, status this cycle, action button.
- Suggested reviewers panel inline on row expand.
- Filter chips: **All · Needs attention · Unassigned · Pending feedback · Scheduled · Completed**. "Needs attention" rolls up unassigned + cycle-deadline approaching + feedback overdue into one view.
- Bulk action: "auto-suggest for all remaining" (preview before commit).

### 8.3 Admin org export (replaces BG Manager / MD dashboard)

There is no in-app dashboard for BG managers or MDs in MVP. The admin can generate a **quarterly org-completion CSV** — one row per cycle × BG × project with completion rate, sessions held, feedback submitted, unmatched counts. The CSV is delivered out-of-band (email or shared drive) on request.

_Phase 3:_ Drill-down to anonymized session feedback themes (top tags from free-text), still admin-only.

Level distribution histograms and cohort-shift views were intentionally dropped to keep the program developmental rather than appraisal-coded. Readers see whether the program is happening, not how individual engineers score.

## 9. Data model (Postgres / Supabase)

Tables (PK omitted, all FKs `on delete restrict`):

- `employees` — imported from sheet #1; one row per person; soft-delete flag.
- `ai_levels` — append-only history `(employee_id, level, source: 'survey'|'override'|'import', set_at, set_by, reason)`. "Current level" is a view = latest row.
- `review_cycles` — `(name, starts_at, ends_at, auto_pair_after, target_filter_json)`.
- `assignments` — `(cycle_id, reviewee_id, reviewer_id, status, proposed_at, accepted_at, scheduled_at, completed_at, calendar_event_id)`.
- `feedback_forms` — `(version, role: 'reviewer'|'reviewee', schema_json)`.
- `feedback_submissions` — `(assignment_id, form_id, author_id, answers_json, submitted_at)`.
- `notifications` — `(employee_id, channel, payload_json, sent_at, status)`. Audit trail.

Row-Level Security: engineers see only their own rows; engineers with coachees additionally see their coachees' rows (resolved at query time from `employees.coach_email`); admins see all. Implemented via Supabase RLS policies tied to a `role` claim on the JWT plus a derived `is_coach_of(user_id, target_id)` helper. No BG/MD tier — the org-completion view is admin-only and emitted as a CSV.

## 10. Integrations

| Need | How | Phase |
|---|---|---|
| Auth | Google OAuth via Supabase Auth, restricted to `@visma.com` domain | MVP |
| Employee/coach import | Server action that pulls Google Sheet #1 via service account (read-only). Nightly + on-demand. | MVP |
| AI level import | Same as above for sheet #2; idempotent upsert on `(email, timestamp)`. | MVP |
| Calendar | Google Calendar `freebusy` + event create on both calendars; user-delegated OAuth scope. | Phase 2 |
| Email notifications | Resend (Vercel marketplace) or transactional via Google Workspace — used for assignment confirmations, reminders, feedback nudges. Not approval. | MVP |
| Auto-pair scheduler | Vercel cron job, daily at 06:00 UTC. | Phase 3 |

## 10b. Email notifications & reminders

Email is the only system-driven channel in MVP (Slack is out). Every send is logged in `notifications` for audit and rate-limiting. Users can opt out of reminders (not transactional confirmations) per category in their profile.

### Trigger matrix

MVP ships with **six** templates. Other notifications listed in earlier drafts (T-1h session reminder, T+5d cc-coach overdue nudge, T-14d coachee-unassigned, separate pairing-recorded transactional, level-changed transactional, import-failed operational) were cut to keep the inbox load light. Pairing details are folded into the cycle-opens email when pairing is recorded before the cycle starts.

| Trigger | Recipient | When | Type | Opt-out? | Template |
|---|---|---|---|---|---|
| Cycle opens (with pairing if recorded) | All targeted engineers + coaches | Day 0 of cycle | Transactional | No | `email-cycle-opens-with-pairing` |
| Session in 24h | Both parties | T-24h before `scheduled_at` | Reminder | Yes | `email-session-24h` |
| Feedback received | Reviewee | Within 5 min of reviewer submit | Transactional | No | `email-feedback-received` |
| Feedback overdue | Author only (no cc-coach) | T+1d after deadline | Reminder | Yes | `email-feedback-overdue` |
| Auto-pair imminent | Coach | T-7d before auto-pair fires | Reminder | Yes | `email-autopair-imminent` |
| Cycle ends, summary | All engineers | Day after cycle ends | Digest | Yes | `email-cycle-summary-digest` |

### Delivery rules

- Daily digest batching: reminder-class emails for the same recipient within 24h are combined into one digest. Transactional sends are always immediate.
- Quiet hours: 18:00–08:00 local (recipient's profile timezone, default Europe/Vilnius). Transactional sends bypass.
- Rate cap: max 3 reminder emails per recipient per day.
- All emails carry: subject line with cycle name + action, plain-text fallback, deep link to the relevant screen, footer with opt-out link per category.
- Failure handling: bounce → mark `notifications.status = 'failed'` and surface on `AdminNotificationsAudit`. Three consecutive failures → flag the employee record.

### Implementation

- **Provider**: Resend (Vercel marketplace). React-Email components for templates, stored in `app/emails/`.
- **Schedule**: Vercel Cron for time-based triggers (daily at 06:00 UTC sweeps T-3d / T-1d / overdue checks; hourly sweep for T-24h / T-1h session reminders).
- **Templates** versioned per cycle; admin can preview before sending.

## 11. Tech stack

- **Framework**: Next.js 16 (App Router, RSC, Server Actions). Cache Components for dashboards.
- **DB & Auth**: Supabase (Postgres + RLS + Google OAuth, hosted via Vercel Marketplace).
- **UI**: Tailwind + shadcn/ui. Visma magenta-red (`#E70641`) as primary, neutral surfaces — verify against Visma brand kit before launch.
- **Hosting**: Vercel (Fluid Compute, default Node runtime).
- **Background jobs**: Vercel Cron for the daily auto-pair sweep; Vercel Queues if Slack/calendar workloads grow.
- **Observability**: Vercel logs + Sentry; product analytics via PostHog (optional).
- **No AI features in v1.** AI-assisted feedback summarization is a Phase 3 consideration; if added, route through Vercel AI Gateway.

## 12. Phasing

### Phase 1 — MVP (target: 4–6 weeks)

- Data import from both sheets.
- Engineer + coach dashboards.
- Manual reviewer assignment (no calendar, no Slack).
- Feedback forms (both sides), e-mail notifications only.
- BG/MD read-only view of completion + level distribution.

**Definition of done:** a coach can log in, see their coachees, assign reviewers, and both parties can submit feedback that shows up on the BG dashboard.

### Phase 2 — Calendar integration (target: +2 weeks)

- Google Calendar freebusy lookup & event creation.
- Notification audit trail.

### Phase 3 — Automation & analytics (target: +3 weeks)

- Auto-pair daily sweep.
- Level-shift cohort analytics.
- Free-text feedback theme extraction (LLM via AI Gateway).
- Admin UI for cycle creation & form versioning.

### Phase 4 — In-app AI Skill Check-in (target: TBD)

The quarterly AI Skill Check-in conversation between coach and coachee currently happens outside this app. Phase 4 adds it as a co-filled form: coach and coachee meet, walk through Toolkit + Workflow questions together, and the resulting `(level, loop_position)` is recorded directly to `ai_levels` with `source = 'checkin'`. This makes the app the source of truth for levels (replacing the external sheet import).

This is a separate workflow from cross-project reviews — same data model, different ceremony.

## 13. Risks & open questions

- **Coach data coverage is sparse** (16% missing). MVP must surface this and let admins fix it in-app.
- **Survey freshness** — 144 unique employees responded vs. ~200 total. Need a re-survey push at cycle start. If no level is on file, the engineer's badge shows "unverified" and they are excluded from matching until their coach records a level from the next AI Skill Check-in.
- **Cross-language naming** — Lithuanian/English mix in sheet #1. Normalize at import; display as-is.
- **Calendar privacy** — `freebusy` only reveals busy/free, no event details, so this is fine; document it explicitly in the privacy notice.
- **GDPR** — feedback is personal data tied to an identified employee. Retention: keep raw feedback 2 years, anonymized aggregates indefinitely. Confirm with Visma DPO.

## 14. Success metrics

- ≥ 80% of eligible engineers complete one cross-project review per cycle.
- ≥ 70% of feedback forms submitted within 7 days of session.
- Year-over-year: median AI level moves upward, with measurable cohort progression (% of engineers who advanced ≥1 level). Target: 30–40% cohort advance per year in early cycles, normalising as the population matures.
- Coaches assign reviewers manually ≥ 70% of the time (auto-pair is the safety net, not the norm).
