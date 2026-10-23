# Mobile Responsiveness — Copilot instructions

When generating or modifying layout, typography, spacing, navigation, forms, grids, or any positioning code, default to mobile-first and verify against the 12-point check below before suggesting completion.

## Critical (never violate)

1. Every page has `<meta name="viewport" content="width=device-width, initial-scale=1">` — never include `user-scalable=no` or `maximum-scale=1`.
2. No element creates horizontal `<body>` scroll at any width ≥320px.

## Required defaults

- Body copy: `font-size: 16px` minimum. Inputs: `font-size: 16px` minimum (smaller triggers iOS auto-zoom).
- Container side padding: `1rem` on phones, scale up at ≥414px.
- Interactive elements: minimum `44px × 44px` tap area, minimum `8px` gap between adjacent targets.
- Images: `max-width: 100%; height: auto;` with intrinsic `width`/`height` attrs to prevent CLS.
- iframes (YouTube/Vimeo): wrap in a `padding-bottom: 56.25%` aspect container with the iframe absolute-positioned inside.
- Wide tables: `<div style="overflow-x:auto">` wrapper.
- Mobile menu: body scroll lock when open, focus trap, ESC + tap-outside + explicit close button.
- Fixed-bottom CTAs: add `padding-bottom: env(safe-area-inset-bottom)`.
- Animations: gate behind `@media (prefers-reduced-motion: reduce)` or provide a no-motion fallback.

## Test breakpoints (mention in code comments where relevant)

320 · 375 · 390 · 414 · 600 · 768 · 820

## Anti-patterns — refuse to generate these

- `font-size: 14px` (or smaller) on body or input text
- `display: none` on real content at mobile breakpoints (use a mobile-appropriate layout instead)
- `position: absolute` for fixed mobile nav (use `position: fixed`)
- `width: 100vw` inside padded containers (causes overflow)
- Forms missing `type="email" / type="tel" / inputmode` attrs
- `user-scalable=no` in viewport meta
- Icon-only buttons under 44×44 px
- Carousels with auto-advance and no pause-on-touch

## When asked "is this mobile responsive?"

Run through the 12-point list before answering. Output Critical / Warnings / Polish severity buckets with concrete fix per item. Never answer "yes" without naming the breakpoints and pages verified.
