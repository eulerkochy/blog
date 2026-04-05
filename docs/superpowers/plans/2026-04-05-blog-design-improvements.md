# Blog Design Improvements Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Elevate the matrix-themed Astro blog from "green on black" to immersive, interactive, and functionally complete.

**Architecture:** All changes are CSS/HTML/Astro template changes with zero new runtime JS dependencies. Shiki (bundled with Astro) handles syntax highlighting. Heading IDs are built into Astro's markdown pipeline. One small client-side `<script>` for the reading progress bar.

**Tech Stack:** Astro 5.x, CSS animations, Shiki, Google Fonts (Space Mono)

---

### Task 1: Config and Dependencies

**Files:**
- Modify: `astro.config.mjs`

- [ ] **Step 1: Update astro.config.mjs with Shiki theme and site URL**

Replace the entire file content with:

```js
// @ts-check

import mdx from '@astrojs/mdx';
import sitemap from '@astrojs/sitemap';
import { defineConfig } from 'astro/config';

// https://astro.build/config
export default defineConfig({
	site: 'https://infinityandbeyond.dev',
	integrations: [mdx(), sitemap()],
	markdown: {
		shikiConfig: {
			theme: 'github-dark',
		},
	},
});
```

- [ ] **Step 2: Install dependencies and verify build**

Run:
```bash
bun install && bun run build
```

Expected: Build completes successfully. Shiki theme is now active for code blocks.

- [ ] **Step 3: Commit**

```bash
git add astro.config.mjs
git commit -m "feat: configure shiki syntax highlighting and update site URL"
```

---

### Task 2: Typography — Space Mono for Headings

**Files:**
- Modify: `src/components/BaseHead.astro`
- Modify: `src/styles/global.css`

- [ ] **Step 1: Add Space Mono font preload to BaseHead.astro**

In `src/components/BaseHead.astro`, after the existing Atkinson font preloads (line 34), add:

```html
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&display=swap" rel="stylesheet" />
```

- [ ] **Step 2: Update CSS variables and heading styles in global.css**

In `src/styles/global.css`, in the `:root` block, after the `--mono` variable (line 31), add:

```css
--heading: "Space Mono", monospace;
```

Then update the heading rule (lines 103-113) to use the new font:

Replace:
```css
h1,
h2,
h3,
h4,
h5,
h6 {
	margin: 0;
	font-weight: 700;
	line-height: 0.98;
	letter-spacing: -0.03em;
}
```

With:
```css
h1,
h2,
h3,
h4,
h5,
h6 {
	margin: 0;
	font-family: var(--heading);
	font-weight: 700;
	line-height: 1.05;
	letter-spacing: -0.02em;
}
```

- [ ] **Step 3: Build and verify**

Run: `bun run build`

Expected: Build succeeds. Headings now render in Space Mono.

- [ ] **Step 4: Commit**

```bash
git add src/components/BaseHead.astro src/styles/global.css
git commit -m "feat: add Space Mono heading font for stronger terminal aesthetic"
```

---

### Task 3: Interactive Texture — Glitch, Hovers, Progress Bar

**Files:**
- Modify: `src/styles/global.css`
- Modify: `src/layouts/BlogPost.astro`

- [ ] **Step 1: Add glitch keyframes and h1 glitch hover to global.css**

In `src/styles/global.css`, after the existing `@keyframes matrix-fall` block (after line 395), add:

```css
@keyframes glitch {
	0%, 100% {
		clip-path: inset(0 0 0 0);
		transform: translate(0);
	}
	20% {
		clip-path: inset(20% 0 60% 0);
		transform: translate(-2px, 1px);
	}
	40% {
		clip-path: inset(60% 0 10% 0);
		transform: translate(2px, -1px);
	}
	60% {
		clip-path: inset(40% 0 30% 0);
		transform: translate(-1px, 2px);
	}
	80% {
		clip-path: inset(10% 0 70% 0);
		transform: translate(1px, -2px);
	}
}

h1 {
	position: relative;
	cursor: default;
}

h1:hover {
	animation: glitch 300ms ease-in-out;
	text-shadow:
		2px 0 var(--accent-cold),
		-2px 0 var(--accent);
}
```

- [ ] **Step 2: Add focus-item hover states to global.css**

In `src/styles/global.css`, before the `@media (max-width: 900px)` block (before line 397), add:

```css
.focus-item {
	transition:
		transform 180ms ease,
		border-color 180ms ease;
}

.focus-item:hover {
	transform: translateY(-2px);
	border-color: var(--line-strong);
}
```

- [ ] **Step 3: Fix code syntax highlighting styles**

In `src/styles/global.css`, update the `pre > code` rule (lines 190-194) to preserve Shiki colors:

Replace:
```css
pre > code {
	padding: 0;
	border: 0;
	background: transparent;
}
```

With:
```css
pre > code {
	padding: 0;
	border: 0;
	background: transparent;
	color: inherit;
}
```

Also update the `code` rule (lines 169-177) to not override Shiki's inline code within `pre`:

After the existing `code` block, the `pre > code` already resets it. But we should ensure `pre` doesn't force its own background over Shiki. Update the `pre` rule (lines 179-188):

Replace:
```css
pre {
	overflow-x: auto;
	padding: 1.25rem 1.4rem;
	border: 1px solid rgba(123, 255, 159, 0.18);
	border-radius: 1rem;
	background:
		linear-gradient(180deg, rgba(123, 255, 159, 0.06), transparent 48%),
		rgba(2, 7, 4, 0.92);
	box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.03);
}
```

With:
```css
pre {
	overflow-x: auto;
	padding: 1.25rem 1.4rem;
	border: 1px solid rgba(123, 255, 159, 0.18);
	border-radius: 1rem;
	background:
		linear-gradient(180deg, rgba(123, 255, 159, 0.06), transparent 48%),
		rgba(2, 7, 4, 0.92) !important;
	box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.03);
}
```

The `!important` ensures our dark background overrides Shiki's default `background-color` on the `pre` element while Shiki's token colors remain intact.

- [ ] **Step 4: Add reading progress bar to BlogPost.astro**

In `src/layouts/BlogPost.astro`, add the progress bar element and script. Inside the `<style>` block (after the existing styles, before `</style>`), add:

```css
.progress-bar {
	position: fixed;
	top: 0;
	left: 0;
	width: 0%;
	height: 3px;
	background: linear-gradient(90deg, var(--accent), var(--accent-cold));
	z-index: 30;
	transition: width 50ms linear;
	box-shadow: 0 0 8px rgba(123, 255, 159, 0.4);
}
```

In the `<body>` tag of `BlogPost.astro` (line 104), add the progress bar as the first child:

After `<body>`, before `<Header />`, add:
```html
<div class="progress-bar" id="progress-bar"></div>
```

After the closing `</html>` tag, add the script:

```html
<script>
	const bar = document.getElementById('progress-bar');
	function updateProgress() {
		const scrollTop = window.scrollY;
		const docHeight = document.documentElement.scrollHeight - window.innerHeight;
		const progress = docHeight > 0 ? (scrollTop / docHeight) * 100 : 0;
		bar!.style.width = `${Math.min(progress, 100)}%`;
	}
	window.addEventListener('scroll', updateProgress, { passive: true });
	updateProgress();
</script>
```

- [ ] **Step 5: Build and verify**

Run: `bun run build`

Expected: Build succeeds. Glitch animation, hover states, progress bar, and syntax highlighting all active.

- [ ] **Step 6: Commit**

```bash
git add src/styles/global.css src/layouts/BlogPost.astro
git commit -m "feat: add glitch effects, hover states, reading progress bar, and syntax highlighting styles"
```

---

### Task 4: Brand Mark Pulse Animation

**Files:**
- Modify: `src/components/Header.astro`

- [ ] **Step 1: Add pulse animation to Header.astro**

In `src/components/Header.astro`, inside the `<style>` block, add a new keyframes animation and update the `.brand-mark` rule.

After the existing `.brand-mark` styles (after line 63, the closing `}` of `.brand-mark`), add:

```css
.brand-mark {
	animation: brand-pulse 4s ease-in-out infinite;
}

@keyframes brand-pulse {
	0%, 100% {
		box-shadow: inset 0 0 18px rgba(123, 255, 159, 0.08);
	}
	50% {
		box-shadow:
			inset 0 0 18px rgba(123, 255, 159, 0.08),
			0 0 12px rgba(123, 255, 159, 0.2),
			0 0 24px rgba(123, 255, 159, 0.08);
	}
}
```

Wait — `.brand-mark` is already defined. We need to add the animation property inside the existing `.brand-mark` rule and add the keyframes separately.

In the existing `.brand-mark` block (lines 51-63), add `animation: brand-pulse 4s ease-in-out infinite;` after `box-shadow`:

Replace:
```css
	.brand-mark {
		display: grid;
		place-items: center;
		width: 2.6rem;
		height: 2.6rem;
		border: 1px solid var(--line-strong);
		border-radius: 999px;
		font-family: var(--mono);
		font-size: 0.8rem;
		letter-spacing: 0.12em;
		color: var(--accent);
		box-shadow: inset 0 0 18px rgba(123, 255, 159, 0.08);
	}
```

With:
```css
	.brand-mark {
		display: grid;
		place-items: center;
		width: 2.6rem;
		height: 2.6rem;
		border: 1px solid var(--line-strong);
		border-radius: 999px;
		font-family: var(--mono);
		font-size: 0.8rem;
		letter-spacing: 0.12em;
		color: var(--accent);
		box-shadow: inset 0 0 18px rgba(123, 255, 159, 0.08);
		animation: brand-pulse 4s ease-in-out infinite;
	}

	@keyframes brand-pulse {
		0%, 100% {
			box-shadow: inset 0 0 18px rgba(123, 255, 159, 0.08);
		}
		50% {
			box-shadow:
				inset 0 0 18px rgba(123, 255, 159, 0.08),
				0 0 12px rgba(123, 255, 159, 0.2),
				0 0 24px rgba(123, 255, 159, 0.08);
		}
	}
```

- [ ] **Step 2: Build and verify**

Run: `bun run build`

Expected: Build succeeds. Brand mark now has a subtle pulsing glow.

- [ ] **Step 3: Commit**

```bash
git add src/components/Header.astro
git commit -m "feat: add pulse glow animation to brand mark"
```

---

### Task 5: Blog Post Polish — Reading Time, ToC, Prev/Next Nav

**Files:**
- Modify: `src/pages/blog/[...slug].astro`
- Modify: `src/layouts/BlogPost.astro`

- [ ] **Step 1: Update [...slug].astro to pass headings, reading time, and prev/next posts**

Replace the entire content of `src/pages/blog/[...slug].astro`:

```astro
---
import { type CollectionEntry, getCollection, render } from 'astro:content';
import BlogPost from '../../layouts/BlogPost.astro';

export async function getStaticPaths() {
	const posts = (await getCollection('blog')).sort(
		(a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf(),
	);
	return posts.map((post, index) => ({
		params: { slug: post.id },
		props: {
			post,
			prevPost: index < posts.length - 1 ? posts[index + 1] : null,
			nextPost: index > 0 ? posts[index - 1] : null,
		},
	}));
}

type Props = {
	post: CollectionEntry<'blog'>;
	prevPost: CollectionEntry<'blog'> | null;
	nextPost: CollectionEntry<'blog'> | null;
};

const { post, prevPost, nextPost } = Astro.props;
const { Content, headings } = await render(post);
const wordCount = post.body ? post.body.split(/\s+/).length : 0;
const readingTime = Math.max(1, Math.ceil(wordCount / 200));
---

<BlogPost
	{...post.data}
	headings={headings}
	readingTime={readingTime}
	prevPost={prevPost ? { title: prevPost.data.title, slug: prevPost.id } : null}
	nextPost={nextPost ? { title: nextPost.data.title, slug: nextPost.id } : null}
>
	<Content />
</BlogPost>
```

- [ ] **Step 2: Update BlogPost.astro layout to accept and render new props**

Replace the entire content of `src/layouts/BlogPost.astro`:

```astro
---
import type { CollectionEntry } from 'astro:content';
import BaseHead from '../components/BaseHead.astro';
import Footer from '../components/Footer.astro';
import FormattedDate from '../components/FormattedDate.astro';
import Header from '../components/Header.astro';

type Props = CollectionEntry<'blog'>['data'] & {
	headings?: { depth: number; slug: string; text: string }[];
	readingTime?: number;
	prevPost?: { title: string; slug: string } | null;
	nextPost?: { title: string; slug: string } | null;
};

const { title, description, pubDate, updatedDate, headings = [], readingTime, prevPost, nextPost } = Astro.props;
const tocHeadings = headings.filter((h) => h.depth === 2 || h.depth === 3);
---

<html lang="en">
	<head>
		<BaseHead title={title} description={description} />
		<style>
			.progress-bar {
				position: fixed;
				top: 0;
				left: 0;
				width: 0%;
				height: 3px;
				background: linear-gradient(90deg, var(--accent), var(--accent-cold));
				z-index: 30;
				transition: width 50ms linear;
				box-shadow: 0 0 8px rgba(123, 255, 159, 0.4);
			}

			.post-shell {
				width: min(1180px, calc(100% - 2rem));
				margin: 0 auto;
				padding: 2.8rem 0 5rem;
			}

			.post-hero {
				display: grid;
				gap: 1.1rem;
				max-width: 54rem;
				padding-bottom: 2rem;
				border-bottom: 1px solid var(--line);
			}

			.post-description {
				max-width: 40rem;
				color: var(--muted);
				font-size: 1.08rem;
			}

			.post-grid {
				display: grid;
				grid-template-columns: minmax(200px, 250px) minmax(0, 1fr);
				gap: 2rem;
				padding-top: 2rem;
			}

			.post-meta {
				position: sticky;
				top: 6.5rem;
				align-self: start;
				display: grid;
				gap: 1rem;
				padding-top: 0.35rem;
			}

			.meta-block {
				display: grid;
				gap: 0.35rem;
				padding: 1rem 0;
				border-top: 1px solid rgba(123, 255, 159, 0.12);
			}

			.meta-block span {
				font-family: var(--mono);
				font-size: 0.72rem;
				letter-spacing: 0.18em;
				text-transform: uppercase;
				color: var(--accent);
			}

			.meta-block strong,
			.meta-block p {
				color: var(--muted);
				font-size: 0.92rem;
				line-height: 1.5;
			}

			.toc {
				padding: 1rem 0;
				border-top: 1px solid rgba(123, 255, 159, 0.12);
			}

			.toc-label {
				font-family: var(--mono);
				font-size: 0.72rem;
				letter-spacing: 0.18em;
				text-transform: uppercase;
				color: var(--accent);
				margin-bottom: 0.6rem;
			}

			.toc ol {
				list-style: none;
				padding: 0;
				margin: 0;
				display: grid;
				gap: 0.35rem;
			}

			.toc a {
				font-size: 0.84rem;
				color: var(--muted);
				text-decoration: none;
				transition: color 180ms ease;
			}

			.toc a:hover {
				color: var(--accent);
			}

			.toc-h3 {
				padding-left: 1rem;
			}

			.article-wrap {
				min-width: 0;
			}

			.post-nav {
				display: grid;
				grid-template-columns: 1fr 1fr;
				gap: 1.25rem;
				margin-top: 3rem;
				padding-top: 2rem;
				border-top: 1px solid var(--line);
			}

			.post-nav-link {
				display: grid;
				gap: 0.5rem;
				padding: 1.2rem 1.4rem;
				border: 1px solid var(--line);
				border-radius: 1.4rem;
				background: var(--surface);
				text-decoration: none;
				transition:
					transform 180ms ease,
					border-color 180ms ease;
			}

			.post-nav-link:hover {
				transform: translateY(-2px);
				border-color: var(--line-strong);
			}

			.post-nav-label {
				font-family: var(--mono);
				font-size: 0.72rem;
				letter-spacing: 0.18em;
				text-transform: uppercase;
				color: var(--accent);
			}

			.post-nav-title {
				color: var(--text);
				font-size: 1rem;
				line-height: 1.4;
			}

			.post-nav-next {
				text-align: right;
				grid-column: 2;
			}

			@media (max-width: 900px) {
				.post-shell {
					width: min(100%, calc(100% - 1.2rem));
					padding-top: 2rem;
				}

				.post-grid {
					grid-template-columns: 1fr;
					gap: 1.5rem;
				}

				.post-meta {
					position: static;
					grid-template-columns: repeat(2, minmax(0, 1fr));
					gap: 1rem;
				}

				.toc {
					grid-column: 1 / -1;
				}

				.post-nav {
					grid-template-columns: 1fr;
				}

				.post-nav-next {
					text-align: left;
					grid-column: 1;
				}
			}

			@media (max-width: 640px) {
				.post-meta {
					grid-template-columns: 1fr;
				}
			}
		</style>
	</head>
	<body>
		<div class="progress-bar" id="progress-bar"></div>
		<Header />
		<main>
			<article class="post-shell">
				<div class="post-hero">
					<p class="eyebrow">Post / longform transmission</p>
					<h1>{title}</h1>
					<p class="post-description">{description}</p>
				</div>
				<div class="post-grid">
					<aside class="post-meta">
						<div class="meta-block">
							<span>Published</span>
							<strong><FormattedDate date={pubDate} /></strong>
						</div>
						{
							updatedDate && (
								<div class="meta-block">
									<span>Updated</span>
									<strong><FormattedDate date={updatedDate} /></strong>
								</div>
							)
						}
						{
							readingTime && (
								<div class="meta-block">
									<span>Reading time</span>
									<p>{readingTime} min read</p>
								</div>
							)
						}
						{
							tocHeadings.length > 0 && (
								<nav class="toc">
									<p class="toc-label">Contents</p>
									<ol>
										{tocHeadings.map((h) => (
											<li class={h.depth === 3 ? 'toc-h3' : ''}>
												<a href={`#${h.slug}`}>{h.text}</a>
											</li>
										))}
									</ol>
								</nav>
							)
						}
					</aside>
					<div class="article-wrap">
						<div class="prose">
							<slot />
						</div>
						{
							(prevPost || nextPost) && (
								<nav class="post-nav">
									{prevPost && (
										<a class="post-nav-link" href={`/blog/${prevPost.slug}/`}>
											<span class="post-nav-label">&larr; Previous</span>
											<span class="post-nav-title">{prevPost.title}</span>
										</a>
									)}
									{nextPost && (
										<a class="post-nav-link post-nav-next" href={`/blog/${nextPost.slug}/`}>
											<span class="post-nav-label">Next &rarr;</span>
											<span class="post-nav-title">{nextPost.title}</span>
										</a>
									)}
								</nav>
							)
						}
					</div>
				</div>
			</article>
		</main>
		<Footer />
	</body>
</html>
<script>
	const bar = document.getElementById('progress-bar');
	function updateProgress() {
		const scrollTop = window.scrollY;
		const docHeight = document.documentElement.scrollHeight - window.innerHeight;
		const progress = docHeight > 0 ? (scrollTop / docHeight) * 100 : 0;
		if (bar) bar.style.width = `${Math.min(progress, 100)}%`;
	}
	window.addEventListener('scroll', updateProgress, { passive: true });
	updateProgress();
</script>
```

- [ ] **Step 3: Build and verify**

Run: `bun run build`

Expected: Build succeeds. Blog post pages now show reading time, table of contents, and prev/next navigation.

- [ ] **Step 4: Commit**

```bash
git add src/pages/blog/\\[...slug\\].astro src/layouts/BlogPost.astro
git commit -m "feat: add reading time, table of contents, and prev/next navigation to blog posts"
```

---

### Task 6: Custom 404 Page

**Files:**
- Create: `src/pages/404.astro`

- [ ] **Step 1: Create 404.astro**

Create `src/pages/404.astro`:

```astro
---
import BaseHead from '../components/BaseHead.astro';
import Footer from '../components/Footer.astro';
import Header from '../components/Header.astro';
---

<!doctype html>
<html lang="en">
	<head>
		<BaseHead
			title="404 — Signal Lost | Infinity & Beyond"
			description="The requested resource could not be found."
		/>
	</head>
	<body>
		<Header />
		<main>
			<div class="lost-shell">
				<p class="lost-code">ERR::404::NODE_UNREACHABLE</p>
				<h1 class="lost-heading" data-text="Signal Lost">Signal Lost</h1>
				<p class="lost-desc">
					The node you requested has been decommissioned, moved, or never
					existed in this transmission layer.
				</p>
				<a class="button-primary" href="/">Return to base</a>
			</div>
		</main>
		<Footer />
	</body>
</html>
<style>
	.lost-shell {
		display: grid;
		gap: 1.5rem;
		justify-items: center;
		text-align: center;
		padding: 6rem 1rem 4rem;
		min-height: calc(100svh - 4.75rem - 8rem);
		align-content: center;
	}

	.lost-code {
		font-family: var(--mono);
		font-size: 0.78rem;
		letter-spacing: 0.22em;
		text-transform: uppercase;
		color: var(--accent);
	}

	.lost-heading {
		position: relative;
		font-size: clamp(4rem, 12vw, 10rem);
		animation: glitch-persistent 3s ease-in-out infinite;
	}

	.lost-heading::before,
	.lost-heading::after {
		content: attr(data-text);
		position: absolute;
		inset: 0;
	}

	.lost-heading::before {
		color: var(--accent-cold);
		clip-path: inset(0 0 65% 0);
		animation: glitch-slice-top 3s ease-in-out infinite;
	}

	.lost-heading::after {
		color: var(--accent);
		clip-path: inset(65% 0 0 0);
		animation: glitch-slice-bottom 3s ease-in-out infinite;
	}

	.lost-desc {
		max-width: 28rem;
		color: var(--muted);
		font-size: 1.05rem;
	}

	@keyframes glitch-persistent {
		0%, 85%, 100% {
			text-shadow: none;
		}
		86% {
			text-shadow:
				2px 0 var(--accent-cold),
				-2px 0 var(--accent);
		}
		88% {
			text-shadow:
				-2px 0 var(--accent-cold),
				2px 0 var(--accent);
		}
		90% {
			text-shadow: none;
		}
		92% {
			text-shadow:
				1px 0 var(--accent-cold),
				-1px 0 var(--accent);
		}
	}

	@keyframes glitch-slice-top {
		0%, 85%, 100% {
			transform: translate(0);
		}
		86% {
			transform: translate(-3px, 0);
		}
		88% {
			transform: translate(3px, 0);
		}
		90% {
			transform: translate(0);
		}
		92% {
			transform: translate(2px, 0);
		}
	}

	@keyframes glitch-slice-bottom {
		0%, 85%, 100% {
			transform: translate(0);
		}
		86% {
			transform: translate(3px, 0);
		}
		88% {
			transform: translate(-3px, 0);
		}
		90% {
			transform: translate(0);
		}
		92% {
			transform: translate(-2px, 0);
		}
	}
</style>
```

- [ ] **Step 2: Build and verify**

Run: `bun run build`

Expected: Build succeeds. A `dist/404.html` file is generated.

- [ ] **Step 3: Commit**

```bash
git add src/pages/404.astro
git commit -m "feat: add custom 404 page with signal lost glitch effect"
```

---

### Task 7: About Page — Terminal Personality

**Files:**
- Modify: `src/pages/about.astro`

- [ ] **Step 1: Rewrite about.astro with terminal-style profile**

Replace the entire content of `src/pages/about.astro`:

```astro
---
import BaseHead from '../components/BaseHead.astro';
import Footer from '../components/Footer.astro';
import Header from '../components/Header.astro';

const profile = [
	{ key: 'Handle', value: 'eulerkochy' },
	{ key: 'Focus', value: 'Systems programming, performance, correctness' },
	{ key: 'Stack', value: 'Rust, C++, Linux' },
	{ key: 'Status', value: 'Online' },
];

const links = [
	{ cmd: 'github', href: 'https://github.com/eulerkochy', label: 'github.com/eulerkochy' },
];
---

<!doctype html>
<html lang="en">
	<head>
		<BaseHead
			title="About | Infinity & Beyond"
			description="About the operator behind Infinity & Beyond."
		/>
	</head>
	<body>
		<Header />
		<main>
			<div class="page-shell about-shell">
				<section class="about-hero">
					<div class="section-heading">
						<p class="eyebrow">About / operator profile</p>
						<h1>whoami</h1>
					</div>
				</section>

				<section class="terminal-block">
					<div class="terminal-header">
						<span class="terminal-dot"></span>
						<span class="terminal-dot"></span>
						<span class="terminal-dot"></span>
						<span class="terminal-title">operator-profile</span>
					</div>
					<div class="terminal-body">
						<div class="terminal-section">
							<p class="terminal-prompt">$ cat /etc/operator.conf</p>
							<dl class="profile-list">
								{profile.map(({ key, value }) => (
									<div class="profile-row">
										<dt>{key}</dt>
										<dd>{value}</dd>
									</div>
								))}
							</dl>
						</div>

						<div class="terminal-section">
							<p class="terminal-prompt">$ cat /var/log/mission.log</p>
							<p class="terminal-output">
								Infinity & Beyond is a personal archive for systems programming
								notes, implementation details, and the occasional hard-earned
								lesson from low-level work. The goal is less content velocity and
								more signal density.
							</p>
						</div>

						<div class="terminal-section">
							<p class="terminal-prompt">$ ls ./topics/</p>
							<ul class="topic-list">
								<li>rust-and-cpp-deep-dives/</li>
								<li>operating-systems-debugging/</li>
								<li>competitive-programming-patterns/</li>
							</ul>
						</div>

						<div class="terminal-section">
							<p class="terminal-prompt">$ cat ./links.txt</p>
							<div class="link-list">
								{links.map(({ cmd, href, label }) => (
									<p>
										<span class="link-cmd">{cmd}</span>
										<a href={href} target="_blank" rel="noopener noreferrer">{label}</a>
									</p>
								))}
							</div>
						</div>

						<p class="terminal-prompt terminal-cursor">$ <span class="cursor">_</span></p>
					</div>
				</section>

				<section class="about-cta">
					<div class="button-row">
						<a class="button-primary" href="/blog">Open the archive</a>
						<a class="button-secondary" href="/rss.xml">Follow via RSS</a>
					</div>
				</section>
			</div>
		</main>
		<Footer />
	</body>
</html>
<style>
	.about-shell {
		padding-top: 3rem;
	}

	.about-hero {
		max-width: 52rem;
		padding-bottom: 2rem;
	}

	.terminal-block {
		border: 1px solid var(--line);
		border-radius: 1.4rem;
		overflow: hidden;
		background: var(--surface-strong);
		max-width: 52rem;
	}

	.terminal-header {
		display: flex;
		align-items: center;
		gap: 0.5rem;
		padding: 0.85rem 1.2rem;
		background: rgba(123, 255, 159, 0.04);
		border-bottom: 1px solid var(--line);
	}

	.terminal-dot {
		width: 0.6rem;
		height: 0.6rem;
		border-radius: 999px;
		background: rgba(123, 255, 159, 0.25);
	}

	.terminal-dot:first-child {
		background: rgba(255, 95, 86, 0.6);
	}

	.terminal-dot:nth-child(2) {
		background: rgba(255, 189, 46, 0.6);
	}

	.terminal-dot:nth-child(3) {
		background: rgba(39, 201, 63, 0.6);
	}

	.terminal-title {
		margin-left: 0.5rem;
		font-family: var(--mono);
		font-size: 0.72rem;
		letter-spacing: 0.14em;
		color: var(--muted);
	}

	.terminal-body {
		padding: 1.4rem 1.5rem;
		font-family: var(--mono);
		font-size: 0.88rem;
		line-height: 1.7;
		display: grid;
		gap: 1.5rem;
	}

	.terminal-section {
		display: grid;
		gap: 0.6rem;
	}

	.terminal-prompt {
		color: var(--accent);
	}

	.terminal-output {
		color: var(--muted);
		padding-left: 1rem;
		font-family: "Atkinson", sans-serif;
		font-size: 0.94rem;
	}

	.profile-list {
		margin: 0;
		padding-left: 1rem;
		display: grid;
		gap: 0.3rem;
	}

	.profile-row {
		display: flex;
		gap: 0.5rem;
	}

	.profile-row dt {
		color: var(--accent-cold);
		min-width: 7rem;
	}

	.profile-row dt::after {
		content: " =";
	}

	.profile-row dd {
		margin: 0;
		color: var(--text);
	}

	.topic-list {
		list-style: none;
		padding-left: 1rem;
		margin: 0;
		display: grid;
		gap: 0.2rem;
		color: var(--accent-cold);
	}

	.link-list {
		padding-left: 1rem;
	}

	.link-cmd {
		color: var(--accent-cold);
		margin-right: 0.8rem;
	}

	.link-cmd::before {
		content: "[";
	}

	.link-cmd::after {
		content: "]";
	}

	.link-list a {
		color: var(--text);
		text-decoration-color: rgba(123, 255, 159, 0.25);
	}

	.cursor {
		animation: blink-cursor 1s step-end infinite;
	}

	@keyframes blink-cursor {
		0%, 100% {
			opacity: 1;
		}
		50% {
			opacity: 0;
		}
	}

	.about-cta {
		padding-top: 2rem;
	}

	@media (max-width: 720px) {
		.terminal-body {
			padding: 1rem 1.1rem;
			font-size: 0.82rem;
		}

		.profile-row {
			flex-direction: column;
			gap: 0;
		}

		.profile-row dt {
			min-width: auto;
		}
	}
</style>
```

- [ ] **Step 2: Build and verify**

Run: `bun run build`

Expected: Build succeeds. About page renders as terminal-style profile.

- [ ] **Step 3: Commit**

```bash
git add src/pages/about.astro
git commit -m "feat: redesign about page as terminal-style operator profile"
```

---

### Task 8: Final Verification

- [ ] **Step 1: Full build**

Run: `bun run build`

Expected: Clean build, no warnings. Check `dist/` output for:
- `dist/404.html` exists
- `dist/about/index.html` exists
- `dist/blog/hello-world/index.html` exists (with syntax-highlighted code block)

- [ ] **Step 2: Dev server smoke test**

Run: `bun run dev`

Visit in browser:
- `/` — Space Mono headings, glitch on h1 hover, focus-item hover states
- `/blog/hello-world/` — progress bar, reading time, ToC, syntax highlighting
- `/about` — terminal profile with blinking cursor
- `/nonexistent` — 404 page with glitch animation
- Header — brand mark pulsing

- [ ] **Step 3: Final commit (if any missed changes)**

```bash
git status
# If clean, skip. If changes remain:
git add -A && git commit -m "chore: final cleanup"
```
