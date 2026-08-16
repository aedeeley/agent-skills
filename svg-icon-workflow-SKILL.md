---
name: svg-icon-workflow
description: Use when adding, choosing, or optimizing icons and SVG illustrations in a JavaScript web project using icon libraries like Iconify or Lucide. Covers icon component setup, optimization, custom SVG illustration integration, and accessibility for icons. Trigger on "add an icon", "svg illustration", icon library questions, or any .svg file work.
---

## Instructions

### Icon library setup (Iconify / Lucide)
- Prefer on-demand icon components over bundling a whole icon font or full icon-set sprite — both Iconify and Lucide support tree-shaken per-icon imports so only used icons ship to the client.
- **Astro projects**: use `astro-icon` (backed by Iconify's JSON icon sets) for zero-JS inline SVG icons: `<Icon name="lucide:map-pin" />`. This renders actual inline `<svg>`, not a JS-hydrated component — no client directive needed for a static icon.
- **React/Vue components**: `lucide-react` / `lucide-vue-next` for a curated single-style set; `@iconify/react`'s `<Icon icon="mdi:home" />` when you need icons spanning multiple icon sets (Material, Tabler, Simple Icons for brand logos, etc).
- Don't mix icon styles (e.g. Lucide's 2px stroke outline icons next to a filled Material icon) within the same UI region — pick one icon family per visual context for consistency.

### Choosing icons
- Check Iconify's icon sets browser (icon-sets.iconify.design) or Lucide's site before assuming an icon doesn't exist — both cover thousands of icons across many styles.
- For brand/company logos (GitHub, Slack, etc.), use `simple-icons` via Iconify (`simple-icons:github`) rather than a generic icon set — brand marks need to match the actual logo, not an approximation.

### Sizing & styling
- Size icons via `width`/`height` props or Tailwind `size-*` utilities, not inline `style` attributes — keeps sizing consistent with the rest of the design system.
- Icon color should generally inherit via `currentColor` (both Lucide and Iconify default to this) so icons pick up `text-*` color utilities from their container rather than needing separate color props.
- Standard sizing scale to reach for: `size-4` (16px) inline with text, `size-5` (20px) for buttons/nav, `size-6` (24px) for standalone/feature icons — avoid arbitrary one-off pixel sizes.

### Custom SVG illustrations (hand-authored/exported)
When a custom illustration (not a library icon) needs to go into the codebase:
- Run it through SVGO before committing — strips editor cruft (Illustrator/Figma metadata, unused `<defs>`, redundant groups) and shrinks file size significantly. `npx svgo input.svg -o output.svg`.
- Check `viewBox` is present and `width`/`height` attributes are removed or set to `100%` so the SVG scales responsively from its container rather than being pixel-locked.
- If the illustration needs to adapt to light/dark mode or theme colors, replace hardcoded hex fills with `currentColor` or CSS custom properties where the design allows, rather than shipping two separate SVG files per theme.
- Large or complex illustrations (multi-path, detailed) belong in `src/assets/` and get imported as components/optimized assets, not pasted as inline `<svg>` markup in every page that uses them.

### Accessibility
- Decorative icons (paired with visible text, e.g. an icon next to a nav label): `aria-hidden="true"`.
- Standalone icons used as the only content of an interactive element (icon-only buttons): the icon itself stays `aria-hidden`, and the accessible name goes on the parent — `<button aria-label="Close menu">`, not on the SVG.
- Meaningful/informational SVG illustrations (not decorative, conveys content): include a `<title>` element inside the SVG or a `role="img"` + `aria-label` on the wrapper.

### Common mistakes to catch
- Pasting raw unoptimized SVG export (with Illustrator metadata, inline styles, hardcoded ids that could collide) directly into a component.
- Icon-only buttons with no accessible name.
- Reaching for a full icon font (e.g. Font Awesome via CDN) when a tree-shaken component library would ship far less JS/CSS for the same result.
