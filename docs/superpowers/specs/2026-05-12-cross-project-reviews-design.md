# Cross-Project Code Reviews — AI Adoption Edition

**Status:** Draft for review
**Date:** 2026-05-12
**Author:** Tomek Szturo (brainstorm w/ Claude)

## 1. Goal

Bring back the practice of 1–2 cross-project code reviews per engineer per year, but refocus the session on **how the reviewee works with AI**, not on the code itself. The reviewer is always someone with a strictly higher AI-adoption level, so each session is a guided exposure to a more advanced way of working.

### Non-goals (explicitly out of scope)

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

`Assignment.status`: `proposed → reviewer_accepted → scheduled → completed → feedback_submitted`. There's also `declined` and `auto_paired`.

## 4. AI adoption level

Single integer 1–5, using the framework already used in the existing survey:

1. No AI / completion only
2. IDE agent, permission per action
3. CLI agent, permissioned
4. Multiple CLI agents in parallel
5. Orchestrator / fleet

**Source of truth:** latest survey response per employee. Re-survey quarterly. Coaches can override (with a reason) — overrides win until the next survey.

**Missing data:** if no survey and no override, default to Level 2 and flag the employee as "level unverified" on the coach dashboard. Coach is nudged to set it before assigning.

## 5. Review cycle workflow

```
[Cycle opens]
   │
   ▼
Coach dashboard lists each coachee + suggested eligible reviewers
   │
   ▼
Coach picks reviewer  ──►  Reviewer notified (Slack DM, fallback email)
                                  │
                                  ├─ accept ──► Calendar slot proposal (3 options) ──► both confirm ──► scheduled
                                  └─ decline ──► back to coach with reason
   │
   │ (if coach inaction)
   ▼
T-7 days before cycle end: system auto-pairs unassigned coachees
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

**Hard constraints** (filter — anyone not matching is excluded):

- `reviewer.level > reviewee.level` (strict — same level allowed only for Level 5 reviewees, since they cap out).
- Different `project` (this is *cross*-project review).
- Reviewer has ≤ 2 active assignments in the current cycle (load cap).
- Pair hasn't reviewed each other in the previous 2 cycles.

**Soft scoring** (rank among eligible):

- Same `company` (legal entity) +1 — Visma has several; cross-company is allowed but HR/contract details make same-company simpler.
- Different tech stack +1 (broader exposure).
- Reviewer level == reviewee + 1 preferred over level + 3 (closer is usually more useful).
- Lower current load preferred.
- "AI Skill Utilization = Skill Fit" preferred over "Overqualified".

The coach sees the top ~5 ranked candidates with a reason ("Level 4, .NET, different BG, 0/2 load"). They pick; they can also search and override.

**Auto-pair** uses the same scoring, takes top-1, and notifies coach + both parties.

## 7. Feedback forms

Two short forms (one per side), filled within 7 days of the session. Anonymized in aggregate views; visible to reviewee + coach in raw form.

### Reviewer → Reviewee form

Mix of Likert (1–5) and short free text:

1. **Observed AI maturity** — what level did this person actually operate at? (1–5; can diverge from declared)
2. **Tool stack** — what AI tooling did they use during the session?
3. **Prompting & context-setting** — strength + one growth area.
4. **Verification habits** — do they trust output blindly, spot-check, or systematically verify?
5. **Agent orchestration** — N/A · emerging · solid · advanced.
6. **Architecture & clean-code** — single Likert + free text. Lower weight than the AI dimensions.
7. **One concrete habit they should adopt from your workflow.**
8. **One thing they're doing well that you'll steal.**

### Reviewee → Reviewer form

1. Was the reviewer's level genuinely higher in practice? (yes / sort-of / no)
2. Top thing learned.
3. What you'll try in the next 2 weeks.
4. Quality of session (Likert).
5. Optional: feedback for the program.

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
| Slack | DM via bot token; interactive buttons hit a Next.js route. | Phase 2 |
| Email fallback | Resend (Vercel marketplace) or transactional via Google Workspace. | Phase 2 |
| Auto-pair scheduler | Vercel cron job, daily at 06:00 UTC. | Phase 3 |

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

### Phase 2 — Calendar + Slack (target: +3 weeks)

- Slack approval flow with buttons.
- Google Calendar freebusy & event creation.
- Notification audit trail.

### Phase 3 — Automation & analytics (target: +3 weeks)

- Auto-pair daily sweep.
- Level-shift cohort analytics.
- Free-text feedback theme extraction (LLM via AI Gateway).
- Admin UI for cycle creation & form versioning.

## 13. Risks & open questions

- **Coach data coverage is sparse** (16% missing). MVP must surface this and let admins fix it in-app.
- **Survey freshness** — 144 unique employees responded vs. ~660 total. Need a re-survey push at cycle start; consider a 60-second in-app self-assessment as a fallback.
- **Cross-language naming** — Lithuanian/English mix in sheet #1. Normalize at import; display as-is.
- **Calendar privacy** — `freebusy` only reveals busy/free, no event details, so this is fine; document it explicitly in the privacy notice.
- **Slack workspace coverage** — confirm all 660 employees are in a single workspace before committing to Slack as primary channel.
- **GDPR** — feedback is personal data tied to an identified employee. Retention: keep raw feedback 2 years, anonymized aggregates indefinitely. Confirm with Visma DPO.

## 14. Success metrics

- ≥ 80% of eligible engineers complete one cross-project review per cycle.
- ≥ 70% of feedback forms submitted within 7 days of session.
- Year-over-year: median AI level +1 across the org.
- Coaches assign reviewers manually ≥ 70% of the time (auto-pair is the safety net, not the norm).
