# Voice & copy — consolidated style guide

**Status:** Canonical tone reference for all product copy.

The spec carries one load-bearing line: **"Record the pairing you agreed on."** That sentence is the tone fixative — it tells the reader the app is a clerk, not an arbiter. Everything else in the product should sound like that line wrote it.

---

## 1. The voice in three sentences

1. **Direct.** Short sentences. Verbs first. No "Please feel free to…".
2. **Engineer-adult.** Assume the reader is a senior practitioner. No exclamation marks, no emoji, no "🎉 You've been paired!". An occasional dry observation is fine.
3. **The app is bookkeeping.** Pairings happen in Slack and hallways; sessions happen on Google Meet; learning happens between two people. The app records, prompts, and nudges — it never approves, congratulates, or judges.

The reference example from the swarm is the email canvas (bundle 11): *"it's the unsung half of why this works"* — warm, dry, technical-adjacent. Aim for that register.

---

## 2. Word and phrase rules

### Use

- **Verbs:** *record, schedule, propose, confirm, review, pair, recommend, override, import, suggest, watch, steal, bring home.*
- **Cycle-specific:** "this cycle," "H1·2026," "the sweep," "auto-paired."
- **Levels:** "L3 · CLI agent" — always the number, optional space, the friendly name. Never "Level 3 (CLI agent)" with parens, never "Tier 3".
- **Counterpart:** the person on the other side of a pairing. "Counterpart" is the canonical noun; "partner," "buddy," and "match" are rejected.
- **Pair / pairing:** the relationship. **Session:** the meeting. **Assignment:** the database row. Don't mix.

### Banned phrases

- "Please" anywhere. (Polite is not Visma; clear is Visma.)
- "We're excited to…", "We're thrilled…", emoji of any kind.
- "Your buddy / partner / match / mentor" — use **counterpart, reviewer, reviewee**.
- "Approve / approval / accept / decline" — the app records, never adjudicates.
- "Mentor / mentee" — this is a peer practice, not a hierarchy.
- "AI Master / AI Pro / AI Wizard" — use the level number and the framework name.
- "Just," "simply," "easy" — assume nothing is easy.
- "Awesome / great / nice" — neutral observations only.
- Marketing tense ("Unlock your potential…").

### Tone in the wild

- **Empty state:** "No past cycles yet. H1·2026 is your first cross-project cycle at Visma. Past sessions and feedback will appear here once H2 opens." (bundle 01) — kept.
- **Auto-pair imminent warning:** "If no one's assigned by Friday, the system pairs them. Here's who they'd get." Concrete, no panic.
- **Feedback overdue:** "Your review of Mantas was 9 days ago. The 7-day window closed 2 days ago. Open the form." — factual, action at the end. (bundle 01) — kept.
- **AssignmentRecorderDialog:** "Record the pairing you've agreed on. We're not approving anything — just writing it down so the calendar and emails know." (bundle 06 + spec §6.6) — kept.

---

## 3. Page intent lines — canonical set

Every screen opens with a one-line page intent under the title (§6.1). One line, declarative, names the audience + the action expected. The swarm produced 11 variants; here is the consolidated set:

| Screen | Page intent (one line, kept as-is in JSX) |
|---|---|
| **LoginScreen** | "Cross-project review workspace for Visma engineers. Use your @visma.com Google account to continue." |
| **EngineerDashboard** | "Your next cross-project session, your current AI level, and anything waiting on you this cycle." |
| **MySessionsList** | "Every session you've been assigned to, in either role, across cycles." |
| **SessionDetail (reviewee)** | "What you'll be reviewed on, who's reviewing you, and how to prepare." |
| **SessionDetail (reviewer)** | "Who you're reviewing, what they're working on, and what they want to walk away with." |
| **FeedbackForm (reviewer)** | "Eight short questions about how this person worked with AI. Drafts autosave." |
| **FeedbackForm (reviewee)** | "Five short questions about how the session went. Drafts autosave." |
| **ProfileScreen** | "Your current AI level, how it changed, and what other engineers have said about you." |
| **SelfAssessmentForm** | "Five questions, sixty seconds. Keeps your level current between surveys." |
| **CoachCoacheeRoster** | "Your coachees this cycle. Pair them with a reviewer one level ahead." |
| **CoacheeDetail** | "One coachee, ranked candidates, and the pairing you've agreed on." |
| **AssignmentRecorderDialog** | "Record the pairing you've agreed on. The app isn't approving — it's writing it down." |
| **CoachAssignmentsBoard** | "Every assignment in this cycle, in either role you wear. Filter to find the ones in flight." |
| **InsightsCompletion** | "How many cross-project reviews actually happened, by BG and project, against the 80% target." |
| **InsightsLevels** | "AI level distribution across the org, filterable by BG, company, project, and role." |
| **AdminCycleList** | "Every cycle the platform has run. Open one to edit metadata, scope, and auto-pair date." |
| **AdminCycleEditor (new)** | "Define a cycle. Population, dates, and the auto-pair fallback date." |
| **AdminCycleEditor (edit-active)** | "Editing a cycle that's already in flight. Some fields are locked; others re-target the population." |
| **AdminImports** | "Pull the latest data from the source sheets. Dry-run by default; review the diff before applying." |
| **AdminLevelOverride** | "Set an engineer's AI level outside the survey. Recorded with reason and audit timestamp." |
| **EmptyState (sessions)** | "No sessions yet this cycle. Your coach will pair you with a reviewer one level ahead." |
| **NotFound** | "We don't have a page at that address. Use the sidebar." |

---

## 4. CTA labels — canonical verbs

| Action | Label | Notes |
|---|---|---|
| Primary positive action | imperative verb (**Open**, **Record**, **Save**, **Pair**, **Schedule**, **Submit**) | Single magenta button per page. |
| Open a record | **Open** ("Open form", "Open prep notes") | Not "View", not "See". |
| Record a pairing | **Record pairing** | Not "Assign", not "Confirm". |
| Pick a reviewer from suggestions | **Pick** | One word. The verb fits the row. |
| Start the auto-suggest preview | **Auto-suggest all remaining** | Bundle's wording — kept. |
| Self-assessment | **Start self-assessment** / **Re-assess** | Both legal depending on context. |
| Confirm a level | **Confirm your level** | Used when level is stale or unverified. |
| Override a level | **Override level** | Admin/coach only. Always followed by a reason field. |
| Cancel / close | **Cancel** (modal) / **Close** (drawer / dismissible card) | Never "Nevermind", never "Back". |
| Destructive | **Delete cycle**, **Discard changes** | Verb + noun. Confirms with a typed-name guard. |

---

## 5. Status labels — pill copy

Always exactly these strings, exactly this casing:

- `Assigned` — a reviewer is recorded; no date yet.
- `Scheduled` — a date is set.
- `Completed` — the session has happened.
- `Feedback submitted` — at least one side has submitted feedback.
- `Auto-paired` — the auto-pair sweep ran (this is a flag, rendered as a separate pill).
- `Unverified` — for levels only, on engineers with no recent survey/override.
- `Stale` — for levels only, when the last survey is >6 months old.

---

## 6. Emails — tone reference

From bundle 11 (kept as canonical):

- **Subject lines:** "Cross-project reviews — Cycle H1·2026 starts today." — em-dash, cycle name, action. No greeting, no exclamation.
- **Hero:** "One review this half. Here's what's coming and what we need from you." — calm, factual, what+why in two clauses.
- **CTAs:** "Open your dashboard," "View the pairing," "Open the feedback form." — `Open` is the universal email verb.
- **Footers (transactional):** "This is a transactional message about your cross-project review. It can't be opted out, but you can manage your account settings."
- **Footers (reminder):** "You're getting this because reminders are on for `<category>`. Unsubscribe from this category, or all reminders."
- **Voice:** "It's the unsung half of why this works." — one dry sentence per email is the maximum. Don't be cute.

---

## 7. Error and failure copy

- **Validation:** name the rule plainly. "Reason is required." not "Please fill in this field." not "Oops!".
- **Server failure:** "Something failed on our side. We've logged it. Try again in a minute, and if it still fails, email `tools@visma.com`."
- **Import failure:** "Sheet `<name>` returned an error at row N. Fix it in the sheet, then re-run import." (bundle 10 — kept.)
- **Permission denied:** "You don't have access to that. If you think you should, ask your coach or `tools@visma.com`."
- Never use the word "Oops". Never apologize on behalf of "us" — just describe what happened.

---

## 8. Microcopy patterns

- **Mono pre-line labels** (uppercase 11px `JetBrains Mono`): `SOURCE · MAR 2026`, `LAST RUN · 2 H AGO`, `CYCLE / DAY 0`. Use mono whenever the label classifies a value as "machine truth" — timestamps, identifiers, version stamps.
- **Inline mono numbers** in body prose are fine: "Last survey was 64 days ago" → "Last survey was `64` days ago." in mono. Use sparingly.
- **Save indicators:** "Draft saved · 12s ago" with a pulse dot. "No edits yet · drafts saved locally" for empty drafts. (bundle 03 — kept.)
- **Counts:** "8 sessions · 6 completed · 2 in flight". Mono if dense, prose if loose.
- **Counterpart attribution:** "With Henrik Sørensen" / "Reviewed by Astrid Nordby" — always start with the preposition that places them in relation to the reader.

---

## 9. Anti-examples (what not to do)

- "Welcome back! Let's get you set up. 🎉"
- "We're excited to introduce cross-project reviews — your gateway to AI mastery!"
- "Click here to view your awesome new dashboard."
- "Buddy assigned successfully. Please reach out to introduce yourself!"
- "Oops! Something went wrong. Please try again or contact support."
- "Your AI level has been upgraded to L3! 🚀"
- "Your reviewer will reach out shortly with next steps."

If any of these appear in a PR, reject it.
