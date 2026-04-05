# Blog Design Improvements Spec

## Context

Astro blog ("Infinity & Beyond") with a matrix/terminal theme. The color system and animations are solid but the design needs more personality, interactivity, and functional polish.

## Changes

### 1. Typography Overhaul

**Heading font:** Replace Atkinson for headings with **Space Mono** (Google Fonts, monospace, angular). Keep Atkinson for body text where its legibility shines.

- Load Space Mono via `@import` or self-host woff2
- Apply to `h1, h2, h3` globally
- Adjust `letter-spacing` and `line-height` for the new font (Space Mono needs tighter tracking than Atkinson)

### 2. Interactive Texture

**Glitch effect on headings:** CSS-only glitch on `h1` hover using `clip-path` and pseudo-elements with offset colored shadows. Subtle - 300ms duration, no seizure risk.

**Reading progress bar:** Fixed top bar on blog post pages. Green gradient (`--accent` to `--accent-cold`), 3px height, driven by `scroll` event calculating `scrollY / (docHeight - viewportHeight)`. Positioned above the sticky header.

**Hover states on focus-items:** Add `translateY(-2px)` + `border-color: var(--line-strong)` transition on hover (matching `.feature-post:hover` pattern already in codebase).

### 3. Custom 404 Page

File: `src/pages/404.astro`

- "Signal Lost" heading with glitch animation (always active, not just hover)
- Scan-line overlay effect
- Error code display: `ERR::404::NODE_UNREACHABLE`
- "Return to base" button linking to `/`
- Monospace aesthetic throughout
- Reuses BaseHead, Header, Footer components

### 4. Code Syntax Highlighting

Astro supports Shiki out of the box. Configure in `astro.config.mjs`:

```js
markdown: {
  shikiConfig: {
    theme: 'github-dark',
  },
},
```

No additional dependencies needed. Shiki is bundled with Astro. The existing `pre` and `code` styles in global.css will layer on top of Shiki's output - keep the border/radius/background but let Shiki handle token colors.

Adjust global `pre > code` to not override Shiki's `color` property.

### 5. Blog Post Polish

**Reading time:** Calculate from markdown content word count. `Math.ceil(wordCount / 200)` minutes. Display in the post metadata sidebar.

**Table of contents:** Extract h2/h3 headings from rendered content. Display as a list in the metadata sidebar below the existing meta blocks. Use anchor links with `id` attributes on headings (Astro's rehype-slug plugin - add `@astrojs/markdown-remark` rehype-slug).

Actually, Astro has built-in heading ID generation. We need `rehype-slug` and `rehype-autolink-headings` for the IDs. Add as rehype plugins in astro config.

**Previous/Next navigation:** Query all posts, find current post index, render prev/next links at the bottom of the article. Style as cards matching the existing feature-post pattern.

### 6. Brand Mark Animation

Add a CSS animation to the `.brand-mark` element in Header:
- Subtle pulse glow on the border/shadow (`box-shadow` animation)
- On hover: briefly flash between "01" and "10" using CSS content swap (requires restructuring to use `::before` pseudo-element for the text)

Simpler approach: just animate the `box-shadow` glow with a slow pulse. The text swap adds complexity for minimal payoff.

**Decision:** Pulse glow only. 4s infinite ease-in-out animation on `box-shadow`.

### 7. About Page Personality

Restructure as a terminal-style profile:
- `whoami` section with key-value pairs (Name, Focus, Stack, Status)
- Use monospace font, green accent labels
- Add placeholder for social links (GitHub, Twitter/X) styled as terminal commands
- Keep the existing content but reframe in the terminal aesthetic
- No photo - the terminal aesthetic doesn't need one

### 8. Fix Config

Update `astro.config.mjs` site URL. Since we don't know the real domain, use a clear placeholder: `https://infinityandbeyond.dev` (user can change later).

## Files Modified

- `astro.config.mjs` - Shiki config, rehype plugins, site URL
- `src/styles/global.css` - Typography, glitch keyframes, progress bar, code style fixes
- `src/components/Header.astro` - Brand mark pulse animation
- `src/components/BaseHead.astro` - Font import for Space Mono
- `src/layouts/BlogPost.astro` - Reading time, ToC, prev/next nav, progress bar
- `src/pages/about.astro` - Terminal-style redesign
- `src/pages/404.astro` - New file
- `src/pages/blog/[...slug].astro` - Pass prev/next post data to layout
- `package.json` - Add rehype-slug, rehype-autolink-headings

## Files NOT Modified

- `src/pages/index.astro` - No changes needed (typography/interaction changes cascade from global CSS)
- `src/pages/blog/index.astro` - No changes needed
- `src/components/Footer.astro` - No changes needed
