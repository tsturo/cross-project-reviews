# Inconsistencies — swarm audit

**Status:** Blunt findings. Each item names which bundle drifted, what wins, and why.

Severity scale:
- **high** — breaks coherence; users reading two screens side by side will feel like they're in different products.
- **medium** — visible drift; one-off glance reveals the difference.
- **low** — cosmetic; only a designer would notice.

---

## 1. Display font split: Inter Tight vs Fraunces

- **Finding:** Half the swarm reached for Fraunces (variable serif) as the display face; half stuck to Inter Tight per §7.
- **Where:**
  - Inter Tight: `01-auth-and-dashboard/*`, `06-coachee-detail/*`, `07-coach-assignments/*`, `08-insights/*`, `09-admin-cycles/*`, `10-admin-imports/*`
  - Fraunces: `02-engineer-sessions/*`, `03-feedback-forms/*`, `04-profile/*`, `05-coach-roster/*`, `11-emails/*`
- **Severity:** **high.**
- **Resolution:** **Inter Tight wins in-product.** §7 reads "Inter (or Visma's brand sans if specified)" and lists "decorative" and "doesn't feel Visma: multi-accent / decorative" as anti-patterns. Fraunces is the bigger personality move but it pushes the product toward "annual report magazine," not "workspace." Fraunces is **retained only for emails** (bundle 11), where reliability across Outlook/Gmail makes the editorial fallback both safer (system serifs degrade gracefully) and tonally better (an email feels more like a bulletin than a button). In-app: Inter Tight everywhere display work happens.

## 2. Body font drift: Inter vs Geist vs IBM Plex Sans

- **Finding:** Three different sans bodies appear across the swarm.
- **Where:**
  - Inter: 01, 02, 06, 07, 08, 09, 10
  - Geist: 03, 04
  - IBM Plex Sans: 05, 11
- **Severity:** **high.**
- **Resolution:** **Inter wins in-product.** Geist and Plex Sans came in as companions to the Fraunces decision (#1). Once Fraunces is rejected, the rationale for those bodies disappears. Inter is universally available, ships in the spec, and the seven bundles that used it cover the dashboard, coach hub, and admin — the highest-traffic surfaces. Plex Sans is **retained for emails only**.

## 3. Surface color: cool #FAFAFA vs warm paper

- **Finding:** Five bundles used a warm off-white surface; six used `#FAFAFA`.
- **Where:**
  - Cool `#FAFAFA`: 01, 02, 06, 07, 08, 09, 10
  - Warm `#FAFAF7` / `#FBFAF7` / `#F6F2EA`: 03, 04, 05
  - Email canvas `#F4F1EC`: 11
- **Severity:** **medium.** The two clusters look visibly different when placed next to each other, but neither is wrong on its own.
- **Resolution:** **Cool `#FAFAFA` wins** in-product, per §7. Warm paper drifts editorial; it pairs with Fraunces and is rejected on the same grounds. Email canvas stays warm (`#F4F1EC`) because a different surrounding chrome (Gmail/Outlook) benefits from a non-SaaS canvas to feel un-corporate.

## 4. Magenta `#E70641` overuse

- **Finding:** Several bundles let magenta leak beyond CTA + brand-anchor into decoration.
- **Where:**
  - `01-auth-and-dashboard/dashboard.html` — radial corner wash on the NextSessionCard (`rgba(225,0,120,0.05)` gradient). §6.3 reserves magenta for primary actions; a decorative wash violates the rule.
  - `01-auth-and-dashboard/dashboard.html` — magenta dot on the "current" timeline node in the mini timeline strip. Borderline (bundle 01 flagged this in `notes.md`); the dashboard already has one magenta primary CTA, so the timeline dot is the second magenta on the page.
  - `09-admin-cycles/cycle-list.html` — CyclePill rendered in pink (`bg-[#FFF1F8] color: #9D0356`), a derivative of magenta as a tint background. §6.3 forbids brand-color-as-background.
  - `04-profile/profile.html` — magenta "trailing punctuation dot" on display headlines, a self-described "quiet brand signature." Decoration.
- **Severity:** **medium.**
- **Resolution:** Magenta strictly = primary CTA + one brand-anchor per page (the V monogram in sidebar, or focus ring, or the "current" / target marker in a chart). Remove the dashboard radial wash, the cycle-pill pink tint (use neutral `#F3F4F6` like every other CyclePill), and the headline punctuation dots. The timeline current-node dot is **kept** — it's the affordance of "you are here" and replaces (not supplements) other magenta on the dashboard if the page already has its primary CTA elsewhere.

## 5. Level palette application: chip-with-dot vs solid fill

- **Finding:** Two distinct treatments of level color exist.
- **Where:**
  - Chip-with-dot (subtle): bundle 01 (everywhere), 06, 07
  - Solid fill (level color = chip background): bundles 03, 04 (hero glyph), 07 (in-table mono codes), 09 (target preview histogram)
- **Severity:** **medium.**
- **Resolution:** **Both are kept, but with a rule:** solid fill only on hero/glyph contexts (the giant L3 numeral on the dashboard LevelBadge card, the level-glyph in the profile hero, the level-diff visualization in the AssignmentRecorderDialog, the level histogram on insights). Everywhere else — list rows, assignment cards, suggestion chips, table cells — use chip-with-dot. Reason: L4 amber and L5 magenta in solid fill compete visually with `--brand-primary` when they appear in dense lists.

## 6. Level diff visualization

- **Finding:** Three different ways to show "reviewer is one level above reviewee."
- **Where:**
  - `06-coachee-detail/assignment-recorder.html` — dotted rule + arrow + `+1 level · sweet spot` caption.
  - `04-profile/profile.html` — `L2 → L3` chip pair with arrow in the LevelTimeline.
  - `01-auth-and-dashboard/dashboard.html` — same `L2 → L3` chip pair in the "Previous" row of the LevelBadge card.
- **Severity:** **low.**
- **Resolution:** **Bundle 06's grammar wins** as the LevelDiffStrip component (see component-library.md item D): two badges, dotted rule with arrow, mono caption beneath. Bundle 01 and 04's inline arrow-between-chips is the compact variant — same colors, same chips, no caption — for dense rows.

## 7. Status pill background: tinted vs solid

- **Finding:** Pill background style drifted.
- **Where:**
  - Tinted (`rgba(<status-rgb>, 0.08)`): bundle 01 — applied consistently.
  - Solid color with white text: bundle 07 (in some places).
  - Lab-style chip with mono label: bundle 03.
- **Severity:** **medium.**
- **Resolution:** **Tinted-background variant wins** (bundle 01). Solid pills compete with level chips and the magenta CTA; tinted pills sit quietly. Mono label is rejected — labels are Inter 11px medium.

## 8. CyclePill treatment

- **Finding:** Three CyclePill flavors.
- **Where:**
  - Neutral chip + green dot + mono countdown: bundle 01.
  - Pink-tinted: bundle 09 (`#FFF1F8` / `#9D0356`).
  - Ink-on-paper: bundle 04.
- **Severity:** **medium.**
- **Resolution:** **Bundle 01 wins** (`pill bg-[#F3F4F6] text-[var(--ink)] border border-[var(--border)]`). The pink tint violates magenta discipline (#4). The ink-on-paper version is fine but the green status dot communicates "active" — keep it.

## 9. Sidebar active-item style

- **Finding:** Three active-item treatments.
- **Where:**
  - Black-fill (`bg-[#111111] color: white`): bundle 01.
  - Soft gray-fill: 06, 07, 08, 10.
  - Colored leading dot: 04.
- **Severity:** **medium.**
- **Resolution:** **Soft gray-fill wins** (`bg-[#F4F5F7]` with `color: var(--ink) font-weight: 600`). Black-fill is too loud for an always-visible chrome element; soft-fill keeps the page's primary CTA as the loudest element on screen. The dot indicator is a redundant signal — drop.

## 10. Table density and row hover

- **Finding:** Row height and hover color drifted.
- **Where:**
  - Row height: 56–60px (bundles 05, 07); 52px (one place in 07); 48px (09).
  - Hover bg: `#FAFAFA` (07), `#F7F4ED` (05 warm), `#FCFCFC` (09), `#FBFBFC` (08).
- **Severity:** **low.**
- **Resolution:** **56px row height** + **`#FAFAFA` hover** as canonical. Warm hover is dropped along with the warm surface.

## 11. Button styles: ghost vs outlined

- **Finding:** Secondary buttons drift.
- **Where:**
  - Bundle 01: `h-9 px-3 border border-[var(--border)] bg-white text-[13.5px] hover:bg-gray-50` — outlined.
  - Bundle 08: `btn-ghost { background: #fff; color: var(--ink); box-shadow: inset 0 0 0 1px var(--border); }` — inset ring.
  - Bundle 03: textareas with no border-box.
- **Severity:** **low.**
- **Resolution:** **Bundle 01's outlined button wins.** `inset box-shadow` produces a near-identical visual but it's not the standard tailwind/shadcn pattern; team consistency favors the explicit border. Button heights canonical: 36px (sm) / 40px (md). Hover background `#F9FAFB` or `#F3F4F6`.

## 12. Copy/voice tone: declarative vs marketing

- **Finding:** Headline tone drifts.
- **Where:**
  - Bundle 01 login: "Two reviews a year. One step up the AI ladder." — punchy, direct.
  - Bundle 04 profile: includes a magenta period as a "quiet brand signature" — performative.
  - Bundle 03 reviewee feedback: lab-notebook prose, very long question stems.
  - Bundle 09 admin: corporate ("Cycle metadata", "target population") — functional but dry.
  - Bundle 11 emails: warm-but-direct ("it's the unsung half of why this works") — matches the spec's "Record the pairing you've agreed on" tone.
- **Severity:** **medium.**
- **Resolution:** Bundle 11's tone is the reference (see `voice-and-copy.md`). The magenta period is dropped (decoration). Bundle 03's serif lab-notebook is rejected stylistically but its **content** discipline (numbered sections, one question per block, autosave indicator copy) is kept. Bundle 09's admin copy is fine for an admin surface.

## 13. Primary CTA per page (§6.3 rule)

- **Finding:** Three bundles render more than one magenta primary on the same page.
- **Where:**
  - Dashboard (01): "Confirm your level" in PageHeader + "Open prep notes" on NextSessionCard + "Start self-assessment" on the soft CTA region. Three magentas.
  - Profile (04): primary CTA + magenta timeline node + magenta headline punctuation.
  - Login (01): only one — passes.
- **Severity:** **medium.** §6.3 says one primary action per page.
- **Resolution:** **Dashboard:** keep the NextSessionCard primary ("Open prep notes") as the magenta — it's the action that matches the page's primary intent. PageHeader CTA becomes a neutral outlined button. The self-assessment CTA in the soft region also becomes neutral. **Profile:** keep the page-level primary as magenta; node and punctuation lose magenta.

## 14. Mono font: JetBrains Mono vs Geist Mono vs IBM Plex Mono

- **Finding:** Three mono fonts in play.
- **Where:** JetBrains Mono (01, 02, 03, 05, 06, 07, 08, 09, 10), Geist Mono (04), IBM Plex Mono (11).
- **Severity:** **low.**
- **Resolution:** **JetBrains Mono wins in-product** (universal in 9 of 11). IBM Plex Mono **kept for emails** (Outlook compatibility). Geist Mono dropped.

## 15. Backdrop pattern

- **Finding:** Inconsistent application of a backdrop texture.
- **Where:**
  - 56px grid: 01 (login), 06 (coachee detail), 10 (mentioned in notes but not strongly applied).
  - Dot grain: 03 (warm paper).
  - Corner magenta wash: 01 (login + dashboard hero card).
  - None: 04, 07, 08, 09.
- **Severity:** **low.**
- **Resolution:** **56px grid on auth + empty/error full-bleed surfaces only** (login, imports-error, app-level 404). Dashboard, lists, insights, forms — flat surface, no backdrop. Drop the corner magenta wash everywhere.

## 16. Card border radius

- **Finding:** Card radius drifted between 8, 10, and 12 px.
- **Where:** 8 (some inputs), 10 (01, 06, 07), 12 (08 insights).
- **Severity:** **low.**
- **Resolution:** **10px for standard cards**, **12px for insight tiles** (where the extra softness sells the "summary" feel). Settled in design-system.md §4.

## 17. Auto-paired chip placement

- **Finding:** Bundle 07 correctly renders `Auto` as a separate pill next to the status pill. Bundle 01 also does this. But the assignment cards in bundle 01 occasionally show only "Assigned" without the auto-paired flag when the assignment is in fact auto-paired.
- **Severity:** **low.**
- **Resolution:** Whenever `assignment.auto_paired === true`, always render the `Auto` pill next to the status pill, regardless of status.

## 18. Avatar treatment

- **Finding:** Two avatar styles.
- **Where:**
  - Initials in a tinted disc (bundle 01: `bg-[#E0F2FE] color-[#075985]` etc., color derived from a hash of name): seven bundles.
  - Initials on a magenta disc for the current user: bundle 01 sidebar/topbar.
- **Severity:** **low.**
- **Resolution:** Hash-derived tinted discs for everyone, including the current user. Magenta avatar disc reserved for **the V monogram only**. Color palette for avatars: a fixed 6-color set from the Tailwind 100/600 pairs (sky, emerald, amber, rose, violet, slate).

## 19. Form input chrome

- **Finding:** Form inputs drift.
- **Where:** Bundle 03's "no border-box" textarea on the reviewee free-text. Bundle 09's filter-builder pill clauses. Bundle 10's mono row-number labels.
- **Severity:** **low.**
- **Resolution:** Default text input: `h-9 px-3 rounded-lg border border-[var(--border)] bg-[var(--surface)] text-[13.5px] focus:bg-white focus:outline-2 focus:outline-[var(--magenta)]`. Textareas: same chrome, `min-h-[88px] p-3`. The "no border" prose textarea on the reviewee feedback is **rejected** — it loses the "you have an editable region here" affordance.

## 20. Empty state illustration choice

- **Finding:** Bundles use a lucide icon in a dashed disc (bundle 01, 07) or omit the visual entirely.
- **Severity:** **low.**
- **Resolution:** Dashed-disc + lucide icon (`archive`, `inbox`, `users`, depending on context). 12×12 disc, `border-dashed border-[var(--border)]`, icon `w-5 h-5 text-[var(--muted)]`.

## 21. Notification badge in TopBar

- **Finding:** Bundle 01 renders a magenta bell badge with count "2". Other bundles omit the bell.
- **Severity:** **low.**
- **Resolution:** Keep the bell + badge in TopBar across all authenticated screens; badge uses magenta (it's an action affordance — "click me, something needs you"). This is allowed under magenta discipline as the topbar is shared chrome, so the badge counts as the single anchor for the chrome strip, not the page.
