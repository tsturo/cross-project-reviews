# 06 — CoacheeDetail & AssignmentRecorder

## Intent

The coach's deep-dive on one coachee. The page must make the right reviewer obvious in under five seconds — ranked candidates with scannable reason chips, no scrolling for the top five. The modal that follows is **not an approval flow**: it records a pairing already agreed in Slack.

## Aesthetic direction

Nordic-clean editorial. Hairline borders, generous whitespace, faint 56px grid backdrop carried over from `login.html`. Inter + Inter Tight pairing; JetBrains Mono for numerics (levels, loads, dates) so data feels factual, not decorative. Magenta `#E10078` appears exactly once on the page (as required color tag on the dialog) and once on the dialog primary CTA — discipline per overview §6.3.

## Key decisions

- **Ranked list, not cards.** Five candidates each in a single row with rank number, name, level, project, company, stack, then a horizontal strip of five reason chips. Scannable in one eye-sweep, zero scrolling at 1280px.
- **Reason chips are typed.** Dot-coloured for level diff, lucide glyphs for stack/company/load/fit, amber-tinted only for the one warning case (`Overqualified`). A pseudo-warning (`Load 2/2 · near cap`) sits inline without elevating to alert chrome.
- **History as timeline, not table.** Sidebar shows three past reviews with reviewer name, cycle code, and the level diff visualised as two badges separated by an arrow — same grammar as the modal.
- **Search section inlined.** Per the brief, `ReviewerSearchDialog` is shown as a full section at the bottom rather than a modal — easier to evaluate the design without state.
- **Modal copy is load-bearing.** "Record the pairing you've agreed on" + the helper line + the required-checkbox copy together tell the coach this is bookkeeping, not approval. The submit button stays disabled until the checkbox is ticked; the `Required` micro-label is the only other magenta on the dialog.
- **Level diff visualised.** In the modal, reviewee→reviewer is a dotted rule with an arrow and `+1 level · sweet spot` — reuses the same visual grammar as the history timeline so coaches read it without thinking.
