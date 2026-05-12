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

| Role | Who | What they do in the app |
|---|---|---|
| **Engineer** | Every employee (~660) | See own upcoming sessions (as reviewer & reviewee), fill feedback forms, view own AI level & history |
| **Coach** | ~100 employees (line managers) | See coachee roster, pick a reviewer per coachee, approve auto-pair suggestions, see status |
| **BG Manager / MD** | ~10 | Org-wide view: completion rates, level distribution, level-shift trends |
| **Admin** | 1–2 platform owners | Manage cycles, override pairings, import data, reset levels |

Roles are derived from the imported data (`Is Coach?` column, `Department Manager`, etc.), not stored as enums on users — except `admin`, which is set manually.

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

Four sections; each ≈ equal weight. Likert (1–5) + short free text.

**Section 1 — Architecture & design patterns**

1. Strength: which architectural decision or pattern did they use well? (free text)
2. Growth area: which architecture / pattern would have served them better? (free text — e.g. "event sourcing for the audit log instead of a side table")
3. Overall architectural awareness (Likert).

**Section 2 — Clean code & engineering hygiene**

4. SOLID / DRY / KISS application (Likert + one example each way).
5. Testability and test coverage of the change (Likert + comment).
6. Code organization & naming (Likert + comment).

**Section 3 — Testing, observability, security**

7. Test strategy fit (unit / integration / contract — Likert + comment).
8. Telemetry & observability (logs, metrics, traces — Likert).
9. Security / threat-model awareness (Likert + comment if flagged).

**Section 4 — AI-powered workflow**

10. Observed AI maturity — what cell on the 9-grid did they operate at? (cell picker + can diverge from declared)
11. Tool stack — what AI tooling did they use? (multi-select)
12. Prompting & context-setting — strength + growth (paired text).
13. Verification habits — radio (blind / spot-check / systematic).

**Section 5 — Wrap-up**

14. One concrete habit (any section) they should adopt from your workflow.
15. One thing they're doing well that you'll steal.

The form is long. We accept that — a real review session is 1–1.5h, the form takes ~10 min. Autosave drafts. Don't require every Likert; sections can be skipped if not observed during the session ("not covered").

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

- Coachee table: name, level, last review date, status this cycle, action button.
- Suggested reviewers panel inline on row expand.
- "Needs attention" filter: unassigned + cycle deadline approaching.
- Bulk action: "auto-suggest for all remaining" (preview before commit).

### 8.3 BG Manager / MD dashboard

- Completion rate per BG, project, cycle.
- AI-level distribution histogram (filterable by BG, company, project, role).
- Level-shift cohort view: % of engineers who moved +1 level cycle-over-cycle.
- Drill-down to anonymized session feedback themes (top tags from free-text).

## 9. Data model (Postgres / Supabase)

Tables (PK omitted, all FKs `on delete restrict`):

- `employees` — imported from sheet #1; one row per person; soft-delete flag.
- `ai_levels` — append-only history `(employee_id, level, source: 'survey'|'override'|'import', set_at, set_by, reason)`. "Current level" is a view = latest row.
- `review_cycles` — `(name, starts_at, ends_at, auto_pair_after, target_filter_json)`.
- `assignments` — `(cycle_id, reviewee_id, reviewer_id, status, proposed_at, accepted_at, scheduled_at, completed_at, calendar_event_id)`.
- `feedback_forms` — `(version, role: 'reviewer'|'reviewee', schema_json)`.
- `feedback_submissions` — `(assignment_id, form_id, author_id, answers_json, submitted_at)`.
- `notifications` — `(employee_id, channel, payload_json, sent_at, status)`. Audit trail.

Row-Level Security: engineers see only their own rows; coaches see coachees' rows; BG/MD see their BG; admins see all. Implemented via Supabase RLS policies tied to a `role` claim on the JWT.

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

| Trigger | Recipient | When | Type | Opt-out? |
|---|---|---|---|---|
| Cycle opens | All targeted engineers + coaches | Day 0 of cycle | Transactional | No |
| Pairing recorded by coach | Reviewer + Reviewee | Immediately | Transactional | No |
| Session in 24h | Both parties | T-24h before `scheduled_at` | Reminder | Yes |
| Session in 1h | Both parties | T-1h | Reminder | Yes |
| Session marked complete, schedule a date | Both parties | If no `scheduled_at` set 7 days into cycle | Reminder | Yes |
| Feedback due in 3 days | Author (reviewer / reviewee) | T-3d before 7-day post-session deadline | Reminder | Yes |
| Feedback overdue | Author + Coach (cc on second nudge) | T+1d and T+5d after deadline | Reminder | Yes |
| Feedback received | Reviewee | Within 5 min of reviewer submit | Transactional | No |
| Coachee still unassigned | Coach | T-14d, T-7d, T-1d before cycle deadline | Reminder | Yes |
| Auto-pair imminent | Coach | T-24h before auto-pair fires | Reminder | Yes |
| Auto-pair fired | Coach + Reviewer + Reviewee | Immediately | Transactional | No |
| Cycle ends, summary | All engineers, BG/MD digest | Day after cycle ends | Digest | Yes |
| Level changed (coach override) | Affected engineer | Immediately | Transactional | No |
| Import failed | Admin | On error | Operational | No |

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
- **UI**: Tailwind + shadcn/ui. Visma magenta-red (`#E10078`) as primary, neutral surfaces — verify against Visma brand kit before launch.
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
- **Survey freshness** — 144 unique employees responded vs. ~660 total. Need a re-survey push at cycle start; consider a 60-second in-app self-assessment as a fallback.
- **Cross-language naming** — Lithuanian/English mix in sheet #1. Normalize at import; display as-is.
- **Calendar privacy** — `freebusy` only reveals busy/free, no event details, so this is fine; document it explicitly in the privacy notice.
- **GDPR** — feedback is personal data tied to an identified employee. Retention: keep raw feedback 2 years, anonymized aggregates indefinitely. Confirm with Visma DPO.

## 14. Success metrics

- ≥ 80% of eligible engineers complete one cross-project review per cycle.
- ≥ 70% of feedback forms submitted within 7 days of session.
- Year-over-year: median AI level +1 across the org.
- Coaches assign reviewers manually ≥ 70% of the time (auto-pair is the safety net, not the norm).
