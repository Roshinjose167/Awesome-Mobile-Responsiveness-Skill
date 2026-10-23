# mobile-responsiveness (OpenCode / AGENTS.md format)

Systematic mobile-responsiveness audit. Catches horizontal scroll, broken nav, illegible text, tap-target violations, form keyboard issues, image/video/table/iframe overflow, and viewport meta misuse.

When the user asks to check mobile, test responsive, audit on phone, or just edited any layout/typography/component code, run the audit.

**Default breakpoints:** 320, 375, 390, 414, 600, 768, 820 — never skip 320.

**Critical (⛔) checks first:** viewport meta tag, no horizontal body scroll. Stop and fix before continuing.

**Then in order:** container gutters, typography & line length, tap targets (44×44 min), forms (16px inputs, type-specific keyboards), navigation (hamburger, sticky offsets), images/video/iframes (max-width:100%, responsive embeds), tables (overflow-x wrappers), fixed/sticky elements (no viewport blockers), modals (focus trap, ESC close), performance & motion (prefers-reduced-motion).

**Output:** Group findings by severity (Critical / Warnings / Polish / Passed). For each: breakpoint(s), symptom, root cause, concrete fix.

Full skill spec: see SKILL.md alongside this file.

Anti-patterns to immediately flag: `display:none` on real content at mobile widths, `font-size < 16px` on body text, fixed-bottom CTAs without `safe-area-inset-bottom`, mobile nav using `position:absolute` instead of fixed, inline SVGs without `max-width:100%`.

Never report "looks fine on mobile" without specifying breakpoints tested AND pages covered.
