---
name: tailwind-conventions
description: Use when writing or reviewing Tailwind CSS classes, setting up design tokens/theme configuration, or styling components in a modern web project (Astro, React, Vue, etc). Covers Tailwind v4 CSS-first config, utility ordering, responsive/state variants, and avoiding common anti-patterns. Trigger on any className/class work, "style this", theme/token questions, or Tailwind config edits.
---

## Instructions

### Tailwind v4 config (CSS-first)
- v4 configures via CSS, not `tailwind.config.js`. Design tokens live in a `@theme` block in your main CSS entry:
  ```css
  @import "tailwindcss";

  @theme {
    --color-brand-500: oklch(0.6 0.15 250);
    --font-display: "Cal Sans", sans-serif;
    --spacing-18: 4.5rem;
  }
  ```
- If the project still has a `tailwind.config.js` with a `theme.extend` block, that's the v3 pattern — flag it and offer to migrate to `@theme` unless the project is intentionally pinned to v3.
- Custom tokens become utilities automatically: `--color-brand-500` → `bg-brand-500`, `text-brand-500`, `border-brand-500`, etc. Don't hand-write custom utility classes for something a token already covers.

### Class ordering (read left to right)
Keep a consistent order so diffs stay readable and classes are scannable at a glance:
1. Layout (`flex`, `grid`, `block`)
2. Positioning (`relative`, `absolute`, `inset-0`)
3. Box model (`w-`, `h-`, `p-`, `m-`, `gap-`)
4. Typography (`text-`, `font-`, `leading-`)
5. Visual (`bg-`, `border-`, `rounded-`, `shadow-`)
6. State/responsive variants last (`hover:`, `focus:`, `md:`, `dark:`)

If a project has Prettier + `prettier-plugin-tailwindcss` installed, defer to its sort order instead of manually ordering — don't fight the formatter.

### Responsive & state variants
- Mobile-first: unprefixed = base/smallest screen, then layer up (`md:`, `lg:`, `xl:`).
- Stack variants directly, no separators needed: `dark:hover:bg-brand-600`.
- Prefer container queries (`@container`, `@sm:`, `@lg:`) over viewport breakpoints when a component's layout should respond to its parent's size rather than the whole page — this is a v4 strength worth reaching for in component libraries.

### Avoiding bloat & anti-patterns
- Don't reach for `@apply` to bundle utilities into a custom class unless the same exact combination repeats 3+ times across unrelated components — otherwise it just recreates the maintenance burden Tailwind exists to avoid.
- Avoid arbitrary values (`w-[437px]`, `text-[#3a3a3a]`) for anything that should be a design decision — push it into a `@theme` token instead so it's reusable and named. Arbitrary values are fine for genuinely one-off layout needs (e.g. `top-[calc(100%+8px)]`).
- Don't mix Tailwind utilities with large hand-written CSS files styling the same components — pick one system per component to avoid specificity fights.

### Accessibility & interaction states
- Every interactive element needs a visible `focus-visible:` state, not just `hover:` — hover-only styling breaks keyboard navigation.
- Use semantic color names in tokens (`--color-danger-500`) rather than raw hues (`--color-red-500`) so intent survives a rebrand.

### Common mistakes to catch
- Missing `dark:` variants on a project that supports dark mode elsewhere.
- Long unordered utility strings that make diffs noisy — reorder per the convention above.
- Redefining spacing/color values inline instead of using existing `@theme` tokens — check `@theme` first before introducing a new magic number.
