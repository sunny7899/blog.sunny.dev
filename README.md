count- 42
pubDatetime next: 23 aug

pnpm run dev
pnpm run build
npx wrangler deploy
npx wrangler versions upload

pnpm install
pnpm install --frozen-lockfile
pnpm approve-build

pnpm dlx @astrojs/upgrade

https://adsenseapprovalchecker.net/

doamin shorten or buy
Top Level Domain Consider using .com, .org, or .net
Analytics Tools - GA4/GTM add google analytics
https://tagmanager.google.com/?authuser=2#/home#tags
work on
HSTS Status
CSP Protection

ALT text on images (simulated check on prominent images)
Provide descriptive alt text for all important images to improve accessibility for visually impaired users and provide context to search engines.

At least 2 pages indexed in Google (simulated)
Ensure your key pages are indexed by Google. Check using 'site:yourdomain.com' search in Google and monitor Google Search Console.
Send Message

Cloudflare R2 

## Project Structure & File Guide

Below is an annotated overview of the project directory and what you can do in each file:

```text
├── Dockerfile                  # Container build instructions
├── astro.config.ts             # Astro/Vite configuration (Tailwind, Shiki syntax highlighter, sitemap, remark plugins)
├── cz.yaml                     # Commitizen commit convention config
├── docker-compose.yml          # Docker Compose configuration for local containerized runs
├── eslint.config.js            # Code linting rules
├── package.json                # Project dependencies and npm scripts
├── pnpm-lock.yaml              # Lockfile for pnpm dependencies
├── pnpm-workspace.yaml         # pnpm workspace configuration
├── wrangler.jsonc              # Cloudflare Workers / Pages deployment configuration
├── tsconfig.json               # TypeScript compiler options and path aliases (@/* -> src/*)
│
├── public/                     # Static assets served at root (e.g. /favicon.svg)
│   ├── _headers                # Custom HTTP response headers for Cloudflare (security headers, caching)
│   ├── favicon.svg             # Website favicon
│   ├── ang.png                 # Default OpenGraph fallback / social preview image
│   └── gde.gif                 # Static animated asset
│
└── src/
    ├── assets/                 # Optimized static assets (processed and compressed by Astro)
    │   └── icons/              # SVG icons used across components
    │
    ├── data/
    │   └── blog/               # Markdown (.md) blog articles (add new posts here)
    │       └── angular/        # Categorized blog posts on Angular
    │
    ├── config.ts               # Global site configuration (title, author, site URL, postsPerPage, timezone)
    ├── constants.ts            # Social media URLs, share links, and navigation constants
    ├── content.config.ts       # Astro Content Collections schema (defines frontmatter fields for blog posts)
    ├── env.d.ts                # TypeScript environment definitions
    ├── remark-collapse.d.ts    # Type definition for the remark-collapse plugin
    │
    ├── components/             # Reusable Astro UI components
    │   ├── Header.astro        # Top navigation bar with logo and dark/light mode toggle
    │   ├── Footer.astro        # Bottom footer with copyright and social links
    │   ├── Card.astro          # Blog post preview card used in post lists
    │   ├── Datetime.astro      # Formats and displays publication & modification dates
    │   ├── Breadcrumb.astro    # Breadcrumb navigation hierarchy
    │   ├── Pagination.astro    # Previous / Next pagination navigation controls
    │   ├── Tag.astro           # Tag badge component linked to /tags/[tag]
    │   ├── ShareLinks.astro    # Social media share buttons under each post
    │   ├── Socials.astro       # Social profile icon list (GitHub, LinkedIn, Twitter, etc.)
    │   ├── BackButton.astro    # "Go back" button for detail pages
    │   ├── BackToTopButton.astro # Floating "Back to Top" scroll button
    │   ├── LinkButton.astro    # Styled anchor/button wrapper
    │   └── EditPost.astro      # "Edit page on GitHub" link at bottom of posts
    │
    ├── layouts/                # Page layout wrappers
    │   ├── Layout.astro        # Root HTML layout (<head>, SEO meta tags, Google Fonts, dark mode script)
    │   ├── PostDetails.astro   # Main layout template for single blog post pages
    │   ├── Main.astro          # Wrapper layout with main content container and heading
    │   └── AboutLayout.astro   # Layout template specifically for the About page
    │
    ├── pages/                  # File-based routing (each file corresponds to a URL route)
    │   ├── index.astro         # Home page (/) with hero section, recent posts, featured posts
    │   ├── about.md            # About Me page (/about)
    │   ├── contact.astro       # Contact page (/contact)
    │   ├── 404.astro           # Not Found error page
    │   ├── privacy.md          # Privacy Policy page (/privacy)
    │   ├── terms.md            # Terms of Service page (/terms)
    │   ├── og.png.ts           # Dynamic OpenGraph image endpoint for the homepage
    │   ├── search.astro        # Client-side post search page (/search)
    │   ├── robots.txt.ts       # Dynamic robots.txt generation
    │   ├── rss.xml.ts          # RSS feed endpoint (/rss.xml)
    │   ├── archives/
    │   │   └── index.astro     # Chronological post archives grouped by year (/archives)
    │   ├── posts/
    │   │   ├── [...page].astro # Paginated list of all posts (/posts, /posts/2)
    │   │   └── [...slug]/
    │   │       ├── index.astro # Individual post page (/posts/[slug])
    │   │       └── index.png.ts # Dynamic OpenGraph social preview image for each post
    │   └── tags/
    │       ├── index.astro     # Tags directory listing all tags (/tags)
    │       └── [tag]/
    │           └── [...page].astro # Posts filtered by specific tag (/tags/[tag])
    │
    ├── scripts/
    │   └── theme.ts            # Client-side script handling dark/light theme switching and persistence
    │
    ├── styles/
    │   ├── global.css          # Base CSS, Tailwind imports, theme color variables (:root / html[data-theme="dark"])
    │   └── typography.css      # Typography & prose styling for markdown content, code blocks, tables
    │
    └── utils/                  # Helper utilities and data processing
        ├── getSortedPosts.ts   # Sorts blog posts by pubDatetime (newest first)
        ├── postFilter.ts       # Filters out drafts and scheduled posts based on margin
        ├── getUniqueTags.ts    # Extracts unique tags from all published posts
        ├── getPostsByTag.ts    # Filters posts by a given tag
        ├── getPostsByGroupCondition.ts # Groups posts by year for the archives page
        ├── getPath.ts          # Normalizes URL paths
        ├── slugify.ts          # Generates URL-safe slugs from post titles or tags
        ├── loadGoogleFont.ts   # Fetches font data for OpenGraph image rendering
        ├── generateOgImages.ts # Generates dynamic SVG/PNG images using Satori and Resvg
        ├── og-templates/       # HTML/SVG templates used for generating OG images
        │   ├── post.js         # Template for individual post OG banner
        │   └── site.js         # Template for site homepage OG banner
        └── transformers/
            └── fileName.js     # Shiki code block plugin to display filename tabs over code snippets
```

---

## How-To Guides

### 1. Adding Images to a Blog Post

There are two recommended ways to add images:

#### Option A: Optimized images in `src/assets/` (Recommended)
Place your image in `src/assets/` (e.g. `src/assets/my-diagram.png`).
In your Markdown post (`src/data/blog/my-post.md`):
```markdown
![Architecture Diagram](../../assets/my-diagram.png)
```
*Astro will optimize the image, convert it to modern formats (AVIF/WebP), and automatically generate responsive sizes.*

#### Option B: Static images in `public/`
Place your image in `public/` (e.g. `public/screenshots/demo.png`).
In your Markdown post:
```markdown
![Demo Screenshot](/screenshots/demo.png)
```
*Served directly without build-time processing. Great for external assets, gifs, or SVGs.*

#### Option C: Post Banner / OpenGraph Image
Set the `ogImage` frontmatter property in your blog post:
```markdown
---
title: "My Article Title"
pubDatetime: 2026-09-03T11:00:00Z
description: "A short summary of the article."
ogImage: "../../assets/cover.png"
---
```

---

### 2. Creating a New Blog Post

Create a new file in `src/data/blog/<post-slug>.md`:

```markdown
---
author: Sunny
pubDatetime: 2026-09-03T11:00:00Z
title: "Your Post Title Here"
featured: false
draft: false
tags:
  - astro
  - cloudflare
description: "A concise summary for search engines and social cards."
---

## Introduction

Your markdown content starts here! You can use:
- Code blocks with syntax highlighting and file titles
- Tables, blockquotes, and lists
- Inline and block images
```

---

### 3. Customizing the Website

- **Site title, author, URL, posts per page**: Edit [`src/config.ts`](src/config.ts)
- **Social profiles (GitHub, LinkedIn, Twitter, etc.)**: Edit [`src/constants.ts`](src/constants.ts)
- **Colors and styling**: Edit [`src/styles/global.css`](src/styles/global.css) and [`src/styles/typography.css`](src/styles/typography.css)
- **Navigation header links**: Edit [`src/components/Header.astro`](src/components/Header.astro)
- **Homepage intro text / hero**: Edit [`src/pages/index.astro`](src/pages/index.astro)
- **About page content**: Edit [`src/pages/about.md`](src/pages/about.md)
- **Deployment settings**: Edit [`wrangler.jsonc`](wrangler.jsonc)