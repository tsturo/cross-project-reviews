# Feedback forms — design notes

## Aesthetic

Editorial lab-notebook, not survey: warm paper background with a faint dot grain, Fraunces serif for headings and section labels (because every label reads like a prompt, not a data slot), JetBrains Mono for system signals (section numerals, status, counters, audience footers), Geist for body. Visma magenta `#E10078` appears exactly once per page — the Submit button — so the eye knows where the form actually ends. Everything else is ink-on-paper.

## Structure (v2.0 — rebalanced)

Both forms reflect the rebalanced spec: AI is one dimension of four, not the headline.

**Reviewer form — 5 sections, 15 questions, ~10–15 minutes.**

- **01 · Architecture & design patterns** — Q1 strength (text), Q2 growth (text), Q3 architectural awareness (Likert).
- **02 · Clean code & engineering hygiene** — Q4 SOLID/DRY/KISS (Likert + paired worked-well/didn't-work examples), Q5 testability (Likert + comment), Q6 organisation & naming (Likert + comment).
- **03 · Testing, observability, security** — Q7 test strategy fit (Likert + comment), Q8 telemetry depth (Likert), Q9 security awareness (Likert + comment-if-flagged).
- **04 · AI-powered workflow** — Q10 observed AI maturity (9-cell grid picker mapping to the in-loop/on-loop matrix), Q11 tool stack (chip multi-select), Q12 prompting strength + growth (paired text), Q13 verification habits (3-card radio).
- **05 · Wrap-up** — Q14 one habit they should adopt, Q15 one thing you're stealing. Both required before submit.

Every dimensional section (01–04) carries a "not covered in this session — skip" affordance in the top-right of its header. Real sessions don't always touch every topic; skipping is signal, not failure. The wrap-up cannot be skipped.

**Reviewee form — 6 questions, short and focused.**

- **Q1** stage-actually-higher (4-card radio: yes / mostly / partially / no).
- **Q2** top thing learned (textarea, helper note that any topic counts).
- **Q3** what you'll try in 2 weeks (textarea, helper that this is shared with the reviewer).
- **Q4** session quality (Likert).
- **Q5** coverage check (chip multi-select: Architecture · Clean code · Testing · Observability · Security · AI workflow · Other) — feeds back into form calibration; renders with 3 selected by default in the mockup.
- **Q6** optional program feedback (textarea, admin-only).

## Likert vs radio vs chips

Three distinct interaction shapes, three distinct semantics — kept visually distinct so the form teaches its own grammar:

- **Likert** — a physical scale on a black rail: 5 detents that snap into place, big Fraunces numerals stacked over short uppercase labels. Reads as "I'm dialling in a value on a continuum."
- **Radio** — full-width labelled cards with a square mark + helper sentence. A categorical choice between qualitatively different states.
- **Chips** — pill-shaped multi-select. Pick any subset; non-exclusive.

The 9-cell AI maturity picker is its own thing — a 2-column grid mirroring the in-loop / on-loop matrix from spec §4, with vehicle glyphs, level labels, an empty cell for the L1 on-loop and L5 in-loop gaps, and a small `DECLARED` tag on the cell matching the reviewee's declared level.

## Mid-completion state

Reviewer form is shown at **9 of 15** answered:

- Section 01 fully answered (Q1, Q2, Q3 all marked).
- Section 02 partially answered — Likerts marked on Q4/Q5/Q6, Q4 has a worked-well example expanded, Q5 and Q6 comments remain collapsed as stubs (`+ Add a comment (optional)`) since the reviewer hasn't typed anything there yet.
- Section 03 partially answered — Q7 Likert + comment expanded; Q8 Likert marked; Q9 untouched.
- Section 04 starting — only Q10 (9-grid cell) selected; chip set has 2 chips marked; Q12 paired text empty; Q13 verification radio not yet chosen.
- Section 05 not started — both wrap-up textareas show placeholders with a required-before-submit warning. Submit button is disabled accordingly.

Reviewee form is shown at **3 of 6** answered:

- Q1 "Yes" selected.
- Q2 and Q3 contain realistic content (DLQ-with-alerting learning; concrete 2-week commitment).
- Q4 Likert untouched.
- Q5 coverage chips show three selected (Architecture, Testing, Observability) with a helper note that AI workflow will be flagged as not-covered on the reviewer's rail.
- Q6 empty (optional).

Submit on the reviewee form remains disabled until Q1–Q5 are answered.

## Coverage tracker (right rail, reviewer form)

The reviewer form adds an `xl` breakpoint right rail that mirrors the section progress: 5 rows (one per section), each showing answered-count and a state (`done` / `active` / `todo`). The currently active section gets a thin magenta marker on the left edge — the only place magenta appears outside the Submit button, used here as a positional aid rather than a CTA. Below the tracker sits a short "form conventions" card explaining Likert vs radio vs skip, and an estimated-time-remaining readout in Fraunces.

## Autosave & progress

Surfaced as a quiet trio in the sticky header: pulse dot, "Draft saved · 8s ago", and the reassuring footnote "autosaves every keystroke". The progress count (`9/15` on the reviewer, `3/6` on the reviewee) sits beside a row of progress squares (done / current / empty). On the reviewer form the 15 cells wrap onto two lines so the row stays compact within the header.

## Sibling forms

Both pages share AppShell, sticky session header layout, the two-column section pattern (large Fraunces numeral + mono section tag on the left, question + sub on the right), the Likert/radio/textarea/chip components, and the audience footer above Submit. The reviewee form is narrower (920 vs 1240), populated mid-fill to show the asymmetry of the post-session flow, and carries an inline anonymised quote from the reviewer's already-submitted feedback. The reviewer form's right rail is the only structural difference, justified by the length (15 questions across 5 sections needs an at-a-glance map that the reviewee form simply doesn't).
