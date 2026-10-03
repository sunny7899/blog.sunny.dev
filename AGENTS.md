# AGENTS.md

## Project Overview
Fast, lightweight, and accessible developer blog (`sunny-blog` / `blog.sunny.dev`) built with Astro Content Collections and deployed to Cloudflare Pages.
- **Tech Stack:** Astro v5/v7, TypeScript (Strict), Tailwind CSS v4 (@tailwindcss/vite), Pagefind (client-side search), Satori & Resvg (OG image generation), Shiki (syntax highlighting), pnpm.
- **Deployment Platform:** Cloudflare Pages / Workers via Wrangler (`wrangler.jsonc`).
- **Core Purpose:** Technical blogging platform with Markdown/MDX authoring, RSS feed generation, automated sitemap, code syntax highlighting, tags, archives, and dark/light mode themes.

## Project Structure & Architecture
- `src/data/blog/`: Markdown posts (`.md`) organized by topics. Frontmatter schema is defined in `src/content.config.ts`.
- `src/components/`: Reusable Astro UI components (e.g., `Header.astro`, `Footer.astro`, `Card.astro`, `Datetime.astro`, `Comments.astro`, `ShareLinks.astro`, `Pagination.astro`).
- `src/layouts/`: Base page layouts (`Layout.astro`, `PostDetails.astro`, `Main.astro`, `AboutLayout.astro`).
- `src/pages/`: Astro file-based routes (`index.astro`, `about.md`, `contact.astro`, `search.astro`, `posts/`, `tags/`, `archives/`, `rss.xml.ts`, `og.png.ts`).
- `src/utils/`: Helper utilities for post filtering (`postFilter.ts`), sorting (`getSortedPosts.ts`), tag grouping, slug generation, and dynamic OpenGraph generation (`generateOgImages.ts`).
- `src/styles/`: Theme styling tokens (`global.css`, `typography.css`).
- `public/`: Static files served directly at root (favicons, Cloudflare `_headers`, OpenGraph fallback assets, and built `pagefind` assets).
- `src/config.ts`: Global blog metadata (title, author, site URL, posts per page, locale).
- `src/constants.ts`: Social links and navigation constants.
- `astro.config.ts`: Integrations (sitemap), Markdown & Shiki syntax configuration, and Vite plugins.
- `wrangler.jsonc`: Cloudflare Pages assets and environment configuration.

## Build, Run & Certified Commands
Always prefer `pnpm` as the package manager for this repository:
- **Install Dependencies:** `pnpm install`
- **Development Server:** `pnpm run dev` (starts local server at `http://localhost:4321`)
- **Type Sync (Astro collections):** `pnpm run sync`
- **Type & Schema Check:** `npx astro check`
- **Full Production Build:** `pnpm run build` (runs `astro check && astro build && pagefind --site dist && ...`)
- **Preview Build:** `pnpm run preview`
- **Lint Code:** `pnpm run lint` (`eslint .`)
- **Check Formatting:** `pnpm run format:check`
- **Fix Formatting:** `pnpm run format` (`prettier --write .`)
- **Deploy (Cloudflare):** `npx wrangler deploy`

## Code Style & Conventions
- **Framework & Components:** Use Astro components (`.astro`) for UI and layouts. Keep client-side JavaScript minimal; leverage Astro's static site generation (SSG) model.
- **TypeScript:** Strict type checking configured via `astro/tsconfigs/strict`. Use path alias `@/*` (e.g., `import { SITE } from "@/config"`). Avoid explicit `any` to satisfy ESLint rules.
- **Styling:** Use Tailwind CSS v4 utility classes and semantic color variables configured in `src/styles/global.css`. Maintain full light and dark mode compatibility (`data-theme="dark"`).
- **Blog Posts:** Must include all required frontmatter properties specified in `src/content.config.ts` (`title`, `pubDatetime`, `description`, `author`, `tags`).
- **Images:** Place optimized images in `src/assets/` and reference via relative paths in markdown; static unoptimized assets go in `public/`.
- **Code Blocks:** Use Shiki-compatible code fences in markdown with file titles (e.g., ````ts title="example.ts"````) and supported annotation comments for diffs and highlights.

## Testing & Quality Gates
- **Type Checking:** Run `pnpm run sync` followed by `npx astro check` before proposing code changes.
- **Linting:** Ensure `pnpm run lint` passes without errors (rules enforce `no-console` and no untyped `any`).
- **Build Verification:** Before deploying or finishing major changes, ensure `pnpm run build` compiles cleanly without broken links or Pagefind indexing failures.

## Security & Constraints
- **Secrets:** Never commit secrets, analytics private keys, or API tokens. Use `.env` or Cloudflare environment variables (`wrangler.jsonc`).
- **Generated Assets:** Do not manually edit files in `dist/` or `public/pagefind/` (they are generated during build).
- **Static Assets:** HTTP headers and security configurations (HSTS, CSP, cache controls) should be maintained in `public/_headers`.

