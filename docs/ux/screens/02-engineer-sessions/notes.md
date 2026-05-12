# Engineer sessions — design notes

**Aesthetic direction.** Editorial-calm Nordic. Fraunces (display serif) for page titles and counterpart names makes the *person* the headline — these screens are about people learning from each other, not about CRUD. Inter handles UI body, JetBrains Mono carries IDs, timestamps, and the small "/sessions" path label so the app feels engineer-native without resorting to terminal cliché.

**Honoring §6.** Magenta `#E70641` is reserved: it appears only on `focus-magenta` outlines and the one primary CTA on the list page ("Propose a slot"). Status pills use the §7 status palette (`#6B7280 / #2563EB / #16A34A / #7C3AED / #D97706`) and every pill carries both an icon and a label. Level badges use the cool→warm L1→L5 ramp and never collide with status colors. Every list and the "Past" tab have a designed `EmptyState`. Every page opens with a one-line intent line under the title.

**Component vocabulary (verbatim from §5).** AppShell, SideNav, TopBar, CyclePill, PageHeader, LevelBadge, StatusPill, AssignmentCard, EmptyState, FilterBar — all marked in HTML comments.

**Two viewpoints.** The reviewee variant (`session-detail-reviewee.html`) frames the session as "what you want to walk away with" and gates feedback as a future obligation. The reviewer variant (`session-detail-reviewer.html`) surfaces the reviewee's prep read-only at the top and reframes the prep textarea as "what you'll demonstrate", plus a small reviewer-guidance card. Both show the MVP date+time inputs and embed a Phase-2 freebusy slot picker spec as an HTML comment.
