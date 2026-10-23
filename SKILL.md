---
name: mobile-responsiveness
description: Systematic mobile-responsiveness audit for websites, apps, dashboards, landing pages, and HubSpot/WordPress themes. Use when the user asks to "check mobile", "test responsive", "audit mobile UX", "fix mobile issues", "is this responsive", inspects a page at narrow widths, mentions phone/tablet/mobile bugs, or after any layout/typography/component change that could regress on small viewports. Catches horizontal scroll, overflow, broken nav, unreachable CTAs, illegible text, tap-target violations, fixed-element collisions, form keyboard issues, viewport meta misuse, and image/video/table/iframe overflow. Not for native iOS/Android app testing.
version: 1.0.0
user-invocable: true
argument-hint: "[url-or-path] [breakpoints?] [report-format?]"
license: MIT
author: Roshin Jose
author_email: roshinjose1@gmail.com
author_phone: "+919645228131"
---

# Mobile Responsiveness Audit

A repeatable, opinionated checklist for catching mobile regressions before users do. Optimized for production websites and themed CMSes (HubSpot, WordPress, Webflow), not native mobile apps.

## When to invoke this skill

Trigger automatically when the user:
- Asks to "check mobile", "test responsive", "audit on phone", "fix mobile bugs"
- Reports a specific symptom (overflow, scroll, hamburger broken, text too small, CTA missing)
- Just finished editing a hero, nav, footer, form, table, modal, or grid — anything that touches layout
- Mentions a viewport size, device name (iPhone, Pixel, iPad), or breakpoint
- Asks for a pre-launch / pre-publish QA pass

Skip when the request is purely backend, server-side, API, or non-UI tooling.

## Phase 1 — Scope & inputs

Before running checks, lock these down:

1. **Target.** A URL (live or staging), a local dev server (`http://localhost:NNNN/path`), or a list of routes. If the user gave a path inside a CMS theme, ask which preview URL renders it.
2. **Breakpoints to test.** Default ladder unless overridden:
   - **320 × 568** — iPhone SE / oldest still-supported
   - **375 × 812** — iPhone 13/14/15 mini, dominant mobile width
   - **390 × 844** — iPhone 14/15/16 standard
   - **414 × 896** — iPhone Plus / Pro Max
   - **600 × 800** — small Android phablet / common CSS breakpoint inflection
   - **768 × 1024** — iPad portrait
   - **820 × 1180** — iPad Air / common landscape edge
   - The user can extend or shorten — but never skip 320 (catches the most issues).
3. **Pages to test.** Always include: home, primary listing/index, one detail/post page, one form page, contact, 404. Add anything the user just touched.
4. **Throttle.** Note whether to test under Fast 3G + 4× CPU throttle. Default: don't throttle unless explicitly asked or perf is in scope.

## Phase 2 — The 12-point check

Walk each page through these in order. **Stop and fix immediately** if a critical issue (marked ⛔) is found; don't continue layering checks on a broken foundation.

### ⛔ 1. Viewport meta tag
- `<head>` must contain `<meta name="viewport" content="width=device-width, initial-scale=1">` (or equivalent).
- Reject `user-scalable=no`, `maximum-scale=1` — accessibility regression.
- Without this, mobile browsers render at 980px and zoom out → everything looks "fine but tiny".

### ⛔ 2. Horizontal scroll on the body
- At each breakpoint, body should NOT scroll horizontally. Check:
  - `document.documentElement.scrollWidth > document.documentElement.clientWidth` → fail.
  - Inspect overflow culprits: unescaped wide elements, fixed widths in px, oversized images, padded grid rows with sub-pixel rounding, `100vw` widths inside padded containers.
- Common fix patterns: `overflow-x: hidden` on body is a band-aid; root cause is usually a child element wider than its container.

### 3. Container side gutters
- Consistent left/right padding on every section. Default minimum: 1rem (16px) on phones, 1.5rem (24px) at ≥414. Tighter than 1rem feels claustrophobic; wider than 2rem wastes content area.
- Check edge-flush components are intentional (full-width hero images, edge-to-edge scrolling chip rows).

### 4. Typography & line length
- Body copy: 16px (1rem) minimum on mobile. Anything ≤14px = fail unless explicitly secondary (captions, eyebrows).
- Headings: h1 should NOT use desktop sizing — `clamp(2rem, 6vw, 4rem)` patterns or explicit mobile overrides.
- Line length: 45–75 characters per line. Verify with the browser's column width × character width. Long paragraphs at narrow widths often need line-height bumps.
- Reject `text-align: justify` on mobile — gappy spacing on narrow columns.

### 5. Tap targets
- Minimum **44 × 44 CSS px** for any interactive element (buttons, links, icons, form inputs).
- Adjacent targets need ≥ 8px gap. Test the chip row, footer nav, social icon row, pagination, mobile drawer items.
- Icon-only buttons (close, menu, search): wrap with padding to hit 44px even if the SVG itself is 16px.

### 6. Forms
- Input `font-size: 16px` or larger — anything smaller triggers iOS auto-zoom on focus, jarring the layout.
- Labels visible above inputs (placeholder-only is an accessibility fail).
- Type-specific keyboards: `type="email"`, `type="tel"`, `inputmode="numeric"`, `autocomplete` attrs set.
- Submit button reachable above the on-screen keyboard — keyboards eat ~50% of viewport on landscape phones.
- Validation errors visible without scrolling.

### 7. Navigation
- Mobile menu (hamburger / drawer / sheet) opens and closes cleanly. Body scroll locked when open.
- CTA button (Talk to an Engineer / Buy Now / Subscribe) reachable from the mobile menu, not only desktop nav.
- Sticky headers: shouldn't cover anchor-scroll targets — verify `#section` deep links land below the header.
- Skip-to-content link present for keyboard users (often missed on themed sites).

### 8. Images, video, iframes
- Every `<img>` has `max-width: 100%` and intrinsic `width`/`height` to prevent CLS.
- Background images don't get cropped to nothing (`background-size: cover` on a 1:3 ratio hero kills the focal point).
- YouTube/Vimeo iframes wrapped in a responsive 16:9 container, not fixed 560×315.
- Native `<video>` elements use `playsinline` and don't break layout when muted autoplay fails.

### 9. Tables
- Wide tables in a horizontally-scrolling wrapper (`overflow-x: auto`) — never let the table push the body wider.
- Consider a stacked card layout for ≤480px if the table has >4 columns.
- Sticky first column for wide spec sheets.

### 10. Fixed/sticky elements
- Floating CTAs, chat bubbles, cookie banners — don't block more than ~12% of viewport, don't overlap form inputs or primary CTAs, dismissible via tap (not just hover-X which is unreachable on touch).
- Sticky filter bars + sticky headers stack correctly — total sticky height shouldn't exceed ~25% of viewport.

### 11. Modals, overlays, drawers
- Trap focus, return focus on close.
- Closable via tap-outside, Escape, AND an explicit close button (any one missing = fail).
- Scroll the modal body, not the page body underneath.
- Account for safe-area insets on notched devices: `padding-bottom: env(safe-area-inset-bottom)`.

### 12. Performance & motion
- Hero LCP image ≤ 100KB on mobile (lossy WebP/AVIF at appropriate width).
- Animations honor `@media (prefers-reduced-motion: reduce)`.
- No layout-thrashing scroll handlers — check for jank on long scroll.

## Phase 3 — Output format

Default report:

```
# Mobile Responsiveness Audit — <Page or URL>

## Critical (fix before publish)
- [ ] <Issue>: <Breakpoint(s)> · <Symptom> · <Likely cause> · <Suggested fix>

## Warnings (fix this sprint)
- [ ] ...

## Polish (nice-to-have)
- [ ] ...

## Passed
- ✓ ...
```

- Group by severity, NOT by phase number.
- For each issue: state the **breakpoint(s) it appears at**, the **observable symptom**, the **probable CSS/HTML root cause**, and a **concrete fix** (selector + property).
- If a fix is non-obvious (e.g., requires restructuring a grid), say so — don't oversimplify.
- Include screenshots/refs to specific elements when the user is in an interactive session.

For a quick pass (user said "quick check"): single-paragraph summary of the worst 3 issues + a count of total findings. Offer the full report on request.

## Phase 4 — Tooling guidance

When the agent has browser tools available, prefer in this order:
1. **Headless browser at each breakpoint** with explicit `viewport: { width, height, deviceScaleFactor: 2, isMobile: true }`. iOS UA string improves accuracy.
2. **DevTools device emulation** screenshots if running locally.
3. **Static CSS analysis** as fallback — grep for `width: \d+px`, `min-width`, missing `max-width: 100%` on images, missing media queries below the desktop breakpoint.

Always:
- Test with realistic content lengths (long product names, very long form labels, 4-line headings) — generic Lorem won't catch the bugs.
- Test BOTH portrait and landscape on phones (landscape catches the keyboard-overlap and reduced-height issues).
- Disable hover-only effects mentally — touch devices don't hover.

## Anti-patterns to flag immediately

- `@media (max-width: 768px) { display: none }` on content the user came for — they shrunk the window, not their interest.
- `font-size: 0.75rem` on body copy.
- `position: fixed; bottom: 0;` floating CTAs without `safe-area-inset-bottom` padding.
- Carousels with auto-advance + no pause-on-touch.
- Mobile nav that uses `position: absolute` instead of fixed (scrolls off-screen).
- Inline SVGs sized in pixels without `max-width: 100%`.
- Tables of contents / sidebars hidden on mobile with no alternative pattern (collapsed accordion, sticky-bottom sheet).

## Hand-off rules

When findings exceed ~15 issues, **batch by component** ("nav", "hero", "product grid", "footer") instead of one giant list — easier to assign and review.

Never report "passes mobile responsive" without specifying the breakpoints tested and pages covered. "Looks fine on my phone" is not an audit.
