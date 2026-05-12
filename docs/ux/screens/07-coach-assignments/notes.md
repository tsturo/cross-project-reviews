# 07 — CoachAssignmentsBoard

## Intent
A coach's full picture of one cycle. The roster view (`/coach/coachees`) is for assigning; this board is for **monitoring** what's already in flight across both hats — coach-of-reviewee and coach-of-reviewer.

## Decisions

- **DataTable, not cards.** 12 rows must fit above the fold without scrolling on a 13" laptop. A card grid would push the most important column (status × deadline) below the metric tiles.
- **Auto-paired is a flag, not a status.** Rendered as a small `Auto` pill **next to** the actual status pill (`Assigned`, `Scheduled`, …). Matches the spec's `auto_paired = true` flag on top of the `assignments.status` enum.
- **Two feedback checkmarks, separate.** Per the brief, never a combined pill. `ck-on` / `ck-off` icons, ordered Reviewer → Reviewee, with tooltips and a legend in the table footer.
- **Role filter as segmented control** — "As coach of reviewee / reviewer" — the most useful slice for a coach who wears two hats on the same screen.
- **Magenta discipline.** Used only on `Record pairing` and `Go to coachees`. Status, level, auto-pair flag all use the spec's prescribed palette; the magenta-adjacent L5 chip is the only place that brushes the brand color, by design.
- **Level chips on reviewers only.** Reviewee level is implied by the matching rule (reviewer is strictly higher); showing both would just add noise. `Lv unverified` appears as a nudge chip on the reviewee where relevant.
- **Empty state** uses the same shell, tiles and filter bar (greyed) so the page doesn't structurally jump when the first row arrives. The CTA pushes to `/coach/coachees` because that's where work actually begins.
