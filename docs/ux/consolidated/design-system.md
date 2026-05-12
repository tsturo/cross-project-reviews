# Design system — consolidated (Stage-3 final pass)

**Status:** Canonical, post-Stage-3 consolidation
**Date:** 2026-05-12
**Scope:** Font stack and surface token set. Aligns all in-app mockups to one visual language without rewriting content or restructuring layouts.
**How to read this:** This file supersedes the earlier Stage-2 consolidated `design-system.md`. The Stage-2 file picked an Inter / cool-white direction; the design lead's Stage-3 decision is to lean into the warm-paper + Fraunces editorial direction that several agents reached for independently. Emails and feedback forms are out of scope for this pass.

---

## 1. Canonical token table

### Fonts

| Token | Stack | Usage |
|---|---|---|
| Display / serif | `'Fraunces', serif` | Page titles, section headlines, hero numerals, anywhere `.font-display` is applied. Optical sizing auto, letter-spacing `-0.02em` for large display, `-0.01em` for body display. |
| UI sans | `'Inter', system-ui, sans-serif` | Body, labels, form controls, nav, tables. Weights 400 / 500 / 600 / 700. |
| Mono | `'JetBrains Mono', monospace` | IDs, timestamps, cycle countdown, level codes (L1–L5), kbd hints, row numbers. Weights 400 / 500 / 600. |

Single Google Fonts import on every in-app page:

```html
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,300;9..144,400;9..144,500;9..144,600&family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet" />
```

### Surfaces, text, hairlines

| Token | Hex | Usage |
|---|---|---|
| `--paper` | `#F6F2EA` | Main app background. Body / html surface. |
| `--paper-warm` | `#FBFAF7` | Cards, inset surfaces, modals, sticky headers, expand-row gradient end. |
| `--ink` | `#131211` | Primary text, primary icons, active nav, dark fills (charts, "you are here"). |
| `--muted` | `#6B6258` | Secondary text, helper text, eyebrow labels. Replaces `#6B7280` / `#6B6760`. |
| `--hairline` | `#E4DED2` | Standard 1px border on cards, dividers, table rows, filter chips, pills, badges. Replaces `#E5E7EB` / `#D1D5DB` / `#E8E5DE` / `#E6E0D8` / `#E8E2D7`. |
| `--magenta` | `#E70641` | Visma brand — primary CTA background and primary accent strokes only. Never as surface or border on neutral pills. |

### Untouched palettes

Level glyph palette (kept from §7 of `01-overview.md`):

| Token | Hex |
|---|---|
| `--l1` | `#64748B` (slate) |
| `--l2` | `#0EA5E9` (sky) |
| `--l3` | `#10B981` (emerald) |
| `--l4` | `#F59E0B` (amber) |
| `--l5` | `#BE185D` (deep magenta) |

Status palette (per `01-overview.md §7` — kept, except the `assigned` neutral which now coincides with `--muted` `#6B6258`):

| State | Hex |
|---|---|
| `assigned` | `#6B6258` (now == muted) |
| `scheduled` | `#2563EB` |
| `completed` | `#16A34A` |
| `feedback_submitted` | `#7C3AED` |
| `auto_paired` | `#D97706` |

### Component conventions

- **Pills / badges / chips** keep their per-component logic (status pills, level badges, hard/soft chips). The consolidation pass only normalized two visual properties:
  - `border-radius: 999px` for all pills.
  - `border-color: #E4DED2` for any chip / pill rendered on neutral chrome.
- **Magenta discipline.** `#E70641` is reserved for primary CTAs and one brand-signature anchor per page (sidebar V monogram, focus ring, "current" timeline node). Never status, never level, never decorative.

---

## 2. Decisions made (one paragraph per category)

**Fonts.** The Stage-2 consolidation picked Inter Tight + Inter + JetBrains Mono, dismissing the serif drift as decorative. Stage-3 reverses that call: the editorial weight of Fraunces is the differentiator that takes this product out of the generic-internal-tool aesthetic and into something that reads as a Visma artifact, while Inter handles all UI density. Every mockup is now on one stack — Fraunces (display), Inter (UI), JetBrains Mono (mono). Inter Tight, Geist, Geist Mono, IBM Plex Sans, IBM Plex Mono, Manrope, and SF Pro have all been swept out. Google Fonts links collapsed to a single canonical import everywhere.

**Surfaces.** The two clusters that emerged in Stage-2 (cool-white `#FAFAFA` / `#FFFFFF` / `#E5E7EB` versus warm paper `#FBFAF7` / `#FAFAF7` / `#F4F1EC` with warmed hairlines) are consolidated onto the warm paper direction. Main background is `#F6F2EA`, cards and inset surfaces are `#FBFAF7`, the canonical hairline is `#E4DED2`. The warm direction better complements Fraunces, reads less SaaS-generic, and lines up with the email canvas tone — making in-app and email feel like one product. Ink lifted to a warmer near-black `#131211`, muted warmed to `#6B6258`.

**Borders and chrome.** Every neutral border drift — `#E5E7EB`, `#D1D5DB`, `#E8E5DE`, `#E8E2D7`, `#E6E0D8` — collapses to the canonical hairline `#E4DED2`. Where mockups defined a `--border-strong` token at `#D1D5DB`, the value is now `#E4DED2`; the variable name is preserved so component CSS still resolves. Roster's deeper warm secondary tones (`#F7F4ED`, `#F1EEE7`, `#4A463F`, `#3A3630`) are left in place — they harmonize with the new ink / muted and act as a coherent warm hover scale.

**Pills, chips, badges.** No structural changes: status pill logic, level badge palette, hard/soft chip behavior all preserved as defined per screen. Only the two visual primitives the design lead called out were normalized: pill radius (`999px`, already true everywhere) and chip border color (`#E4DED2`). Magenta is never used as a neutral pill border.

**Type scale and layout.** Untouched. Each agent's choices for type size, line-height, spacing, and grid stand. The consolidation is purely token alignment, not redesign.

---

## 3. Files touched (21 HTMLs)

All under `/Users/tomek/dev/cross-project-reviews/docs/ux/screens/`. Out of scope: `11-emails/` (table-layout / web-safe-font constraints) and `03-feedback-forms/` (already aesthetically aligned to the new direction).

| File | Summary |
|---|---|
| `01-auth-and-dashboard/dashboard.html` | Swapped Inter Tight display → Fraunces; surfaces `#FAFAFA`/`#FFFFFF`/`#E5E7EB`/`#1A1A1A`/`#6B7280` → canonical warm tokens. |
| `01-auth-and-dashboard/login.html` | Same swap. Hero panel and form column now sit on paper / paper-warm. |
| `02-engineer-sessions/sessions-list.html` | Inter + Fraunces preserved; Tailwind config palette retuned (surface, ink, muted, line). Inline `#FAFAFA` body bg → `#F6F2EA`. |
| `02-engineer-sessions/session-detail-reviewee.html` | Same — Inter sans-serif body now sits on `#F6F2EA`, hairlines `#E4DED2`. |
| `02-engineer-sessions/session-detail-reviewer.html` | Same. |
| `04-profile/profile.html` | Body sans Geist → Inter; Geist Mono → JetBrains Mono. `--surface` `#FAFAF7` → `#F6F2EA`; `--ink` `#111111` → `#131211`; `--line` `#E5E7EB` → `#E4DED2`. |
| `04-profile/self-assessment.html` | Same Geist → Inter / Geist Mono → JetBrains Mono sweep plus canonical tokens. |
| `05-coach-roster/roster-normal.html` | IBM Plex Sans → Inter (Tailwind config + inline). Body bg `#FBFAF7` → `#F6F2EA` (paper); inset surfaces kept on `#FBFAF7` (paper-warm). Hairlines `#E8E5DE` → `#E4DED2`. |
| `05-coach-roster/roster-autopair-warning.html` | Same. |
| `06-coachee-detail/coachee-detail.html` | Inter Tight display → Fraunces. Surface tokens migrated. |
| `06-coachee-detail/assignment-recorder.html` | Same. |
| `07-coach-assignments/assignments-board.html` | Inter Tight → Fraunces. Surfaces, hairlines, ink, muted migrated. |
| `07-coach-assignments/assignments-empty.html` | Same. |
| `08-insights/insights-completion.html` | Inter Tight → Fraunces; surface set migrated. SVG label colors `#9CA3AF` left as muted-2; ink labels switched to `#131211`. |
| `08-insights/insights-levels.html` | Same. |
| `09-admin-cycles/cycle-list.html` | Inter Tight → Fraunces; surface/border/ink/muted tokens migrated. |
| `09-admin-cycles/cycle-editor-new.html` | Same. |
| `09-admin-cycles/cycle-editor-edit-active.html` | Same. |
| `10-admin-imports/imports.html` | Same. |
| `10-admin-imports/imports-error.html` | Same. |
| `10-admin-imports/level-override.html` | Same. |

---

## 4. What this pass did NOT touch

- `11-emails/*` — table layouts, web-safe fonts, email-client constraints. Separate spec.
- `03-feedback-forms/*` — already on Fraunces + Inter + JetBrains Mono with warm paper; no drift.
- Level palette colors (`--l1`..`--l5`).
- Status palette colors except `assigned`, which moves to muted `#6B6258` by virtue of replacing the old `#6B7280` token.
- Type scales, line-heights, spacing, radius, layout, content, copy, DOM structure, component logic.
- Magenta hover state `#C70069` / `#C20068` and its rgba glow on primary buttons.
