# Feedback forms — design notes

## Aesthetic

Editorial lab-notebook, not survey: warm paper background with a faint dot grain, Fraunces serif for headings and section labels (because every label reads like a prompt, not a data slot), JetBrains Mono for system signals (section numbers, status, counters, audience footers), Geist for body. Visma magenta `#E10078` appears exactly once per page — the Submit button — so the eye knows where the form actually ends. Everything else is ink-on-paper.

## Likert vs radio

The Likert is intentionally not a row of radio buttons. It's drawn as a physical scale: a black rail across the card, five detents that snap into place, big Fraunces numerals stacked over short uppercase labels. The interaction reads as "I'm dialing in a value on a continuum." Radios, by contrast, are full-width labeled cards with a square mark + helper sentence — a categorical choice between qualitatively different states. Putting them in the same form makes the semantic difference visible: scale = how much, card = which one.

## Autosave

Surfaced as a quiet trio in the sticky header: pulse dot, "Draft saved · 12s ago", and the reassuring footnote "autosaves every keystroke". On the reviewee form, the same slot reads "No edits yet · drafts saved locally" — same component, different state, no anxiety. The progress count (e.g. `5/8`) sits beside an 8-cell row of progress squares (done / current / empty), giving both an at-a-glance and an exact reading.

## Sibling forms

Both pages share AppShell, sticky session header layout, the two-column section pattern (numbered marker on the left, question on the right), the Likert/radio/textarea components, and the audience footer above Submit. The reviewee form is narrower (920 vs 1080), populated empty to contrast the reviewer's mid-completion draft, and carries an inline anonymised quote from the reviewer's already-submitted feedback — the only structural difference, justified by the asymmetric flow.
