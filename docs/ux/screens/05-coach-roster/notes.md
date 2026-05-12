# Coach roster — design notes

## Aesthetic direction
Editorial-clinical Nordic: Fraunces (variable serif) for display + IBM Plex Sans + JetBrains Mono for IDs/dates/loads. Warm paper background (`#FBFAF7`) and warm hairlines (`#E8E5DE`) instead of cold gray — feels less SaaS-template, more Visma annual report. Magenta is reserved strictly for the single primary CTA per page (Assign reviewer / Pick reviewer / Assign them now).

## Density without crowding
8+ rows handled via: hairline separators (no zebra), 56–60px row height, generous 16px cell padding, and a 4-tile meta strip above the table to absorb summary cognition so the table itself can stay terse. Counterpart names live as secondary `text-[11px]` below the status pill, not as separate columns — keeps the column count at 6. Status pills carry an icon-dot + label and have a quiet `text-[11px]` second line for context (assigned reviewer, scheduled time, "+1 level shift recorded") — that pattern is what unlocks density: one cell, two facts.

## Inline expand × filter bar
The `ReviewerSuggestionPanel` is a full-width row inserted directly after the expanded coachee row (`colspan=6`, indented 60px to align with the avatar column). The filter bar is sticky at the top of the scroll container — when the expand is open and the coach scrolls to evaluate candidates, status/level/project filters stay reachable. The expand panel re-states the coachee context in its own header ("Ranked for Eglė — L4, Visma.net Expense") so the inline view is self-contained even when the source row scrolls out of frame. A "Search all reviewers" link sits in both the panel header and footer.

## Auto-pair warning variant
Dark inverted `NudgeBanner` with magenta accent glow + a serif live countdown (23:47 h·m). Roster is re-sorted: 3 urgent rows pulled to the top with a left rail accent stripe and a stripe-warn group header that carries a bulk "Assign them now" action; everything else moves below a "Already paired" divider so the unassigned three never get lost. Each urgent row previews who they'd be paired with if the sweep runs — makes the consequence concrete.

## Punted
Search-all-reviewers dialog (lives on a future screen), keyboard nav between candidates, mobile layout (table assumes ≥1024px coach desktop use), real avatars, level legend popover.
