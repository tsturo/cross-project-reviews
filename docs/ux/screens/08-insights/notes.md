# Insights — implementation notes

## Charts without a library

Every chart is hand-authored SVG with a fixed `viewBox` so it scales to container width while preserving label kerning. The completion bar chart uses a single shared coordinate system (y0 = 290 = 0%, y = 10 = 100%) with grid lines at 25-pt steps and a magenta dashed line at the 80% target so each bar is read against a constant scale. Bar shade is a deliberate signal — darker bars exceed target, lighter bars sit further below — so the chart still communicates priority in greyscale. The level histogram pairs solid current-cycle bars with dashed-outline previous-cycle ghosts in the same level color, letting one frame carry the comparison. Sparklines and stacked bars in tables are inline SVG / flex divs — no JS, no canvas, no chart runtime.

## Filter bar persistence

The `FilterBar` is the same component on both screens: BG, Company, Project, (Role on levels), and Cycle, plus a Reset and a small "Filters persist in URL & per-user" hint. On a real app these become URL query params (`?bg=public&cycle=h1-2026`) so a filtered link is shareable, and the last-used set is stored per user so a BG manager lands back where they left off. Saved views ("Org overview") show how a named filter set survives across sessions.

## Level palette discipline

`L1 #64748B · L2 #0EA5E9 · L3 #10B981 · L4 #F59E0B · L5 #BE185D` is used identically to the engineer-facing `LevelBadge` — same slate→teal→green→amber→deep-magenta sequence, never reordered, never reused for status. Magenta `#E70641` appears exactly twice per screen: the primary Export CTA and the 80%-target line.
