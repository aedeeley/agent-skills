---
name: astro-conventions
description: Use when creating or editing Astro components, pages, layouts, content collections, or routing in a JavaScript/TypeScript web project. Covers .astro file structure, islands architecture (client directives), content collections, file-based routing, and API endpoints. Trigger on any .astro file work, "new page", "new component", "content collection", or Astro-specific questions.
---

## Instructions

### File structure defaults
- Pages go in `src/pages/` — file path = route. Dynamic routes use `[param].astro` or `[...slug].astro`.
- Reusable UI goes in `src/components/`, one component per file, PascalCase filenames (`Card.astro`, `Header.astro`).
- Shared page shells go in `src/layouts/`. Every page should wrap content in a layout via `<Layout>` slot pattern, not copy-paste `<html>` boilerplate.
- Static assets that need processing (images, fonts) go in `src/assets/`; things served as-is go in `public/`.

### Component script conventions
- Keep the frontmatter script (`---`) focused on data fetching, prop typing, and logic — no inline business logic that belongs in a util.
- Always type props explicitly:
  ```astro
  ---
  interface Props {
    title: string;
    variant?: 'primary' | 'secondary';
  }
  const { title, variant = 'primary' } = Astro.props;
  ---
  ```
- Prefer `Astro.props` destructuring with defaults over optional-chaining scattered through the template.

### Islands architecture (client directives)
- Astro components are zero-JS by default. Only add a client directive when the component truly needs interactivity.
- Directive choice matters — default to the least aggressive one that works:
  - `client:load` — needed immediately (nav menus with state, auth-gated UI)
  - `client:idle` — can wait until the browser is idle (non-critical widgets)
  - `client:visible` — below the fold, hydrate on scroll into view (galleries, comment sections) — usually the right default for content-heavy pages
  - `client:media="(...)"` — only hydrate at a breakpoint (mobile-only interactive nav)
- Never add a client directive to a component with no interactivity — that's a common source of unnecessary JS shipped to the client. Flag this if you see it.

### Content collections
- Use `src/content/config.ts` with `defineCollection` + Zod schemas for any structured content (blog posts, case studies, docs).
- Query with `getCollection()` / `getEntry()`, never hand-roll frontmatter parsing.
- Validate schema fields match what templates actually consume — don't leave unused schema fields or untyped `any` frontmatter.

### API endpoints
- `src/pages/api/*.ts` exporting `GET`, `POST`, etc. Return `Response` objects directly (Astro's endpoint API is a thin wrapper over the Fetch API, not Express-style `req/res`).
- Set `export const prerender = false;` on any endpoint or page that needs to run per-request (forms, dynamic data) — Astro defaults to static/prerendered.

### Styling integration
- Scoped `<style>` blocks are the Astro default and are usually right for one-off component styles.
- When the project uses Tailwind (common case here), prefer utility classes in the template over scoped `<style>` for anything reusable — see the tailwind-conventions skill for utility patterns. Reserve scoped `<style>` for things Tailwind genuinely can't express cleanly (complex keyframe animations, pseudo-element art direction).

### Common mistakes to catch
- Using `client:load` everywhere "to be safe" — bloats JS bundle, defeats the point of islands.
- Fetching data in a child component's frontmatter when it could be fetched once in the page and passed down as props — avoids duplicate requests during SSG.
- Forgetting `prerender = false` on dynamic API routes deployed to a static/hybrid output target, causing build-time failures or stale data.
