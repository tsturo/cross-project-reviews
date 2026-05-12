# 09 · Admin Cycles — design notes

**Screens:** `AdminCycleList`, `AdminCycleEditor` (new + edit-active).

## Key decisions

1. **AppShell consistency.** Sidebar groups Workspace / Admin / Insights so admins who also wear an engineer hat don't switch shells. Cycles is the active item; Imports / Overrides / Forms sit beside it (matches §1 sitemap and component vocabulary §5).
2. **Cycle list is a working table, not a gallery.** Status, completion %, target size and auto-pair date are all scannable at row level. The summary strip (Active / Planned / Closed / Sweep) gives admins their morning-check answer above the fold.
3. **Filter builder = readable sentence, not SQL.** Each rule is a pill that reads as English: "last cross-project review was before 2025-08-01 AND BG is any of … excluding role is Engineering Manager". Familiar from Segment / Linear views; no boolean trees, no field-operator-value rows.
4. **Live preview = number + L1–L5 histogram.** The L1→L5 bar uses the official level palette (slate→teal→green→amber→magenta-adjacent). The edit screen adds a "now vs. after" overlay so admins see exactly which 13 engineers their change adds.
5. **Editing-an-active-cycle warning is structural, not just visual.** The amber banner classifies every editable field into three buckets — *re-targets population* / *affects auto-pair only* / *safe* — and each field carries an inline lock chip that repeats its bucket. Start date is hard-disabled (81 sessions completed against it).
6. **Magenta discipline.** Magenta `#E10078` only on "Create cycle", "Save changes", and the cycle pill. Status uses its own palette; the active-cycle warning is amber, never magenta.
7. **Sticky sidebar in editor** keeps Save / pending-changes count / impact summary in view at any scroll depth — common admin pattern for long forms.
