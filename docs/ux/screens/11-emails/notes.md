# Email mockup notes — H1·2026

## Tone choices

Every template treats engineers as adults. No exclamation marks, no emoji, no "🎉 You've been paired!". The voice is warm but engineering-direct: short sentences, occasional dry humor ("it's the unsung half of why this works"), no manager-speak. Magenta `#E10078` is reserved for the single primary action per email — never decorative. A pale ivory canvas `#F4F1EC` outside the email card replaces the default Outlook/Gmail grey to feel like a printed bulletin rather than a notification.

The serif (Fraunces, italic for emphasis) carries the editorial weight; IBM Plex Sans handles body; IBM Plex Mono handles metadata labels and the "cycle pill" tags. This typographic hierarchy is what keeps each email from feeling like a SaaS-default transactional blast.

## Opt-out treatment

Spec §10b separates transactional emails (no opt-out) from reminders, digests, coach nudges (opt-out per category). Footers reflect this honestly — transactional emails say "cannot be opted out" plainly; reminder emails include a category-specific link (`?category=session_reminders`, `?category=feedback_reminders`, `?category=coach_nudges`, `?category=cycle_digest`) alongside the catch-all. Unsubscribe is always present per CAN-SPAM/GDPR; address line and reply-to note are there too.

## Avoiding the wall-of-text digest

The cycle summary uses magazine conventions: an "Issue 02" mast line, three numbered chapters (§ I, § II, § III), 2px section rules with magenta marginalia, a pull-quote in the user's own words, a mega-stat (`L2 → L3`), a 3-row stat table, and a tiny CSS-bar histogram. The reader gets a personal hero before any org numbers, and each chapter is < 200 words. The HTML comment at top notes that the BG/MD variant reuses these components verbatim with different data.

## Email-client compatibility

Tables-only layout, inline styles on every element, 600px container, web-safe fallback stack (`-apple-system,BlinkMacSystemFont,'Segoe UI',Helvetica,Arial,sans-serif`), the Google Fonts `<style>` block as progressive enhancement only (Outlook/Yahoo will fall back to system serifs/sans cleanly), no JS, no background images, no flex/grid, no SVG icons, `role="presentation"` on every layout table, `border-collapse` set where needed, and a single media query for ≤620px stacking. The mockup also includes an inbox-style subject-preview strip so the file looks correct when opened in a browser.

## Punts

- No dark-mode variant (spec calls out "Doesn't feel Visma: dark mode-first").
- No localized strings — copy is English only; Lithuanian/Swedish translations would come from the i18n pipeline.
- The cycle digest's histogram uses HTML divs as bars. A high-fidelity build would swap in pre-rendered PNG bars for Outlook 2016/2019 fidelity.
- Calendar `.ics` attachment for the T-24h email is mentioned in the CTA copy ("both calendars update automatically") but not actually attached.
