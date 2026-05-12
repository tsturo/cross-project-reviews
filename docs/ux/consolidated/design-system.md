# Design system — consolidated

**Status:** Canonical, post-Stage-2 swarm consolidation
**Scope:** Tokens, type, color, spacing, radius, elevation, iconography, backdrop
**How to read this:** Where the eleven swarm bundles disagreed, this file picks one winner and explains why. Future implementation in code must use these tokens verbatim. The spec in `01-overview.md §7` is the parent; this file resolves the ambiguities §7 left open.

---

## 1. Type stack

The swarm split roughly in half. Six bundles (01, 06, 07, 08, 09, 10) used **Inter + Inter Tight + JetBrains Mono** — the literal reading of §7 ("Inter (or Visma's brand sans if specified)"). Five bundles (02, 03, 04, 05, 11) reached for **Fraunces** (variable serif) as the display face, paired with Geist or IBM Plex Sans for body — an editorial direction nobody asked for but several agents reached for independently.

**Winner: Inter + Inter Tight + JetBrains Mono.**

```
--font-display : 'Inter Tight', 'Inter', system-ui, sans-serif
--font-body    : 'Inter', system-ui, sans-serif
--font-mono    : 'JetBrains Mono', ui-monospace, SFMono-Regular, Menlo, monospace
```

- Display (`Inter Tight`) — page titles, hero numerals (level glyphs, KPI tiles), card section headlines. `letter-spacing: -0.02em`.
- Body (`Inter`) — everything else. Weights used: 400 / 500 / 600 / 700.
- Mono (`JetBrains Mono`) — IDs, timestamps, cycle countdown, level codes (`L3`), row numbers, kbd hints. Weights 400 / 500. Mono is reserved for **values that prove something happened**.

**Why Inter, not Fraunces.** §7 explicitly lists "decorative" as anti-Visma, and §6.3 reserves a single accent color. Fraunces is a beautiful face but it pushes the product toward "magazine," not "workspace." The Inter Tight + Inter pair is the most disciplined Nordic reading of §7. Bundles 02/03/04 are still useful — their numerals, ladders, and labels translate cleanly to Inter Tight; only the serif H1s need to change.

**Type scale (px):** 11 · 12 · 13 · 14 · 16 · 18 · 20 · 24 · 30 · 34 · 48. Line height 1.5 body, 1.15–1.25 headings. Uppercase eyebrow labels render at 11–12px with `letter-spacing: 0.16em`.

---

## 2. Color tokens

Surface drifted hardest. The swarm produced two clusters:

- **Cool-white cluster (Inter bundles):** surface `#FAFAFA`, card `#FFFFFF`, border `#E5E7EB`. Matches §7 verbatim.
- **Warm-paper cluster (serif bundles):** surface `#FBFAF7` (05) / `#FAFAF7` (04) / `#F6F2EA` (03) / `#F4F1EC` (11 email canvas). Hairlines warmed to `#E8E2D7` / `#E6E0D8`.

**Winner: cool-white surface, with one warm exception.** The product is a workspace, not a publication. `#FAFAFA` is what §7 prescribes; the warm tones drift toward editorial. The single exception is **the email canvas** (bundle 11) — emails sit in third-party clients and benefit from a warmer outside-the-card color to feel un-SaaS. That stays warm. In-app, all surfaces are cool.

### Canonical token list

| Token | Hex | Usage |
|---|---|---|
| `--surface` | `#FAFAFA` | App background. In-product only. |
| `--surface-warm` | `#F4F1EC` | **Email canvas only** — outside the 600px container. |
| `--card` | `#FFFFFF` | Card / panel / modal background. |
| `--ink` | `#1A1A1A` | Primary text, primary icons, dark fills (charts, "active" nav). |
| `--ink-2` | `#374151` | Secondary text used inside dense rows. |
| `--muted` | `#6B7280` | Body labels, helper text, eyebrow labels. |
| `--muted-2` | `#9CA3AF` | Faintest text (timestamps inside chips, divider mono labels). |
| `--border` | `#E5E7EB` | Standard 1px hairline. |
| `--border-strong` | `#D1D5DB` | Hover-state borders on buttons, focus borders, filter active. |
| `--hairline` | `rgba(17,17,17,0.06)` | Card inner ring (`box-shadow: inset 0 0 0 1px`). Use instead of a border when you don't want a paint. |
| `--focus-ring` | `#E10078` (2px outline, 2px offset) | Keyboard focus only. Never aria-decorative. |
| `--brand-primary` | `#E10078` | Visma magenta. Reserved per §6.3 — see "Magenta discipline" below. |
| `--brand-primary-hover` | `#C70069` | Hover state for primary buttons. |
| `--brand-primary-deep` | `#BE185D` | Used **only** as the L5 level color and as the magenta "target" line on insights charts. Coincides with `--level-5`. |
| `--status-assigned` | `#6B7280` | Assignment in `assigned` state. |
| `--status-scheduled` | `#2563EB` | `scheduled`. Tinted background: `rgba(37,99,235,0.08)`. |
| `--status-completed` | `#16A34A` | `completed`. Tinted background: `rgba(22,163,74,0.08)`. |
| `--status-feedback` | `#7C3AED` | `feedback_submitted`. Tinted background: `rgba(124,58,237,0.08)`. |
| `--status-autopair` | `#D97706` | `auto_paired` flag (independent of status enum). Tinted background: `rgba(217,119,6,0.10)`. |
| `--level-1` | `#64748B` | Slate — no AI / completion only. |
| `--level-2` | `#0EA5E9` | Sky — IDE agent. |
| `--level-3` | `#10B981` | Emerald — CLI agent. |
| `--level-4` | `#F59E0B` | Amber — multiple CLI in parallel. |
| `--level-5` | `#BE185D` | Deep magenta — orchestrator / fleet. |
| `--ok` | `#16A34A` | Generic success (import succeeded). |
| `--warn` | `#D97706` | Generic warning (import warnings, near-cap). |
| `--err` | `#DC2626` | Generic error (import failed). |
| `--info` | `#2563EB` | Generic info. |

### Magenta discipline

`#E10078` appears **at most twice per screen**:

1. The page's single primary CTA.
2. One brand-signature anchor per page: the magenta `V` monogram in the sidebar header, the focus ring (keyboard-only), or the "target" / "current" marker on a chart (insights screens). Bundles 04 and 09 also use it as the "current node" of timelines — acceptable as a "you are here" affordance, not decoration.

Never use magenta for status, level, hover backgrounds, card chrome, or decorative glow. Bundle 01's hero-card radial wash (`rgba(225,0,120,0.05)` corner gradient) is **rejected** as decoration; the NextSessionCard uses a flat white card.

---

## 3. Spacing scale

Tailwind defaults across the board, with one consistent omission: nobody used `4` (1px) and `40` (10rem). Canonical scale:

```
4 · 8 · 12 · 16 · 20 · 24 · 32 · 40 · 48 · 64
```

- Card padding: `24` (`p-6`) on dashboards, `20` (`p-5`) on dense tables.
- Section gap inside a page: `28–36` (`mt-7` / `mt-9`).
- Row height in tables: `56–60px` (bundle 05's choice; bundle 07 used 52 — round up to 56).
- Grid gutter: `20` (`gap-5`).
- Page max-width: `1240px` (bundle 01) — adopt as the canonical content cap.

---

## 4. Radius scale

| Token | Value | Usage |
|---|---|---|
| `--radius-sm` | `4px` | Kbd chips, level codes on dense tables, sparkline bars. |
| `--radius-md` | `8px` | Buttons, inputs, filter chips, nav items. **Default.** |
| `--radius-lg` | `10–12px` | Cards, panels, modals. Bundles drifted between 10 and 12 — settle on **10px** for cards and **12px** for elevated/insight cards. |
| `--radius-pill` | `9999px` | Pills, badges, avatars, progress fills, focus dots. |

`rounded-xl` (1rem) appears in two bundles for hero numeral tiles (the L3 fill on the dashboard, the level-glyph backplate). Acceptable, but call it `--radius-tile: 14px` and use only for the level glyph backplate.

---

## 5. Elevation

Visma is flat. The swarm correctly trended that way; the two outliers (bundle 01's hero radial wash, bundle 04's `0 8px 24px -10px` on primary buttons) are decoration. Canonical:

- **No drop shadows on resting surfaces.** Cards are `1px solid var(--border)` or `inset 0 0 0 1px var(--hairline)`.
- **Primary button hover only:** `0 8px 24px -8px rgba(225,0,120,0.45)`. This is the only shadow in the system.
- **Modal/dialog:** `0 24px 64px -16px rgba(17,17,17,0.18)` over a `rgba(17,17,17,0.4)` scrim. No blur.
- **Sticky bars (top nav, sticky filter bar, modal headers):** no shadow; rely on a 1px bottom border.
- **No glassmorphism, no backdrop-filter, no inner glows.** §7 says it explicitly.

---

## 6. Iconography

Eleven bundles, eleven uses of `lucide@latest` via the umd bundle — universal agreement. **Winner: Lucide.** No heroicons, no phosphor, no custom SVG icon library.

- Size convention: `w-3.5 h-3.5` (14px) in dense table cells, `w-4 h-4` (16px) in body and pills, `w-5 h-5` (20px) in card headers, `w-6 h-6` (24px) for empty-state illustrations.
- Stroke is the lucide default (`stroke-width: 2`). Never thin them.
- The lucide icons used as inline pill glyphs are sized `12px` via the `.pill > svg { width:12px; height:12px }` rule (bundle 01).
- Emails use **no SVG icons** (bundle 11 was correct) — substitute IBM Plex Mono unicode glyphs or skip.

---

## 7. Backdrop pattern

Five bundles applied some backdrop texture: bundle 01 (faint 56px grid + corner magenta wash), bundle 06 (same 56px grid carried over), bundle 03 (warm paper dot grain), bundle 04 (off-white surface no pattern), bundle 11 (warm canvas, no pattern).

**Winner: faint 56px grid, no magenta wash.**

```css
.backdrop {
  background-image:
    linear-gradient(transparent 0, transparent calc(100% - 1px), rgba(0,0,0,0.025) 100%),
    linear-gradient(90deg, transparent 0, transparent calc(100% - 1px), rgba(0,0,0,0.025) 100%);
  background-size: 56px 56px, 56px 56px;
}
```

Applied to the login page and to authenticated full-bleed surfaces (`/login`, `/admin/imports/error`, empty states). The dashboard, lists, insights, forms — all of these sit on a flat `--surface` with no backdrop. The corner magenta wash from bundle 01 is **rejected** (decoration). The warm dot grain from bundle 03 is **rejected** (editorial drift).

---

## 8. Motion & interaction

The swarm converged here: `transition: background 120ms ease, box-shadow 120ms ease, transform 120ms ease` on the primary button, `translateY(1px)` on `:active`. Adopt as canonical. No page-load animations, no shimmer skeletons. Loading is a single spinner or a `Skeleton` block — same neutral surface as `--border`.

---

## 9. Tokens-as-CSS-vars boilerplate

Every screen file should open with this `:root` block, in this order. The swarm already used very similar lists — this is the consolidated minimum:

```css
:root {
  --font-display: 'Inter Tight', 'Inter', system-ui, sans-serif;
  --font-body:    'Inter', system-ui, sans-serif;
  --font-mono:    'JetBrains Mono', ui-monospace, monospace;

  --surface: #FAFAFA;
  --card: #FFFFFF;
  --ink: #1A1A1A;
  --ink-2: #374151;
  --muted: #6B7280;
  --muted-2: #9CA3AF;
  --border: #E5E7EB;
  --border-strong: #D1D5DB;
  --hairline: rgba(17,17,17,0.06);

  --magenta: #E10078;
  --magenta-hover: #C70069;

  --l1: #64748B;  --l2: #0EA5E9;  --l3: #10B981;  --l4: #F59E0B;  --l5: #BE185D;

  --s-assigned: #6B7280;
  --s-scheduled: #2563EB;
  --s-completed: #16A34A;
  --s-feedback:  #7C3AED;
  --s-autopair:  #D97706;
}
```
