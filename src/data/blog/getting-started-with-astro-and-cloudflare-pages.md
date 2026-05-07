---
title: Getting Started with Astro and Cloudflare Pages
author: Sunny
pubDatetime: 2026-05-06T04:06:31Z
slug: getting-started-with-astro-and-cloudflare-pages
featured: false
draft: false
tags:
  - TypeScript
  - Astro
description:
  If you are hunting for the ultimate web development stack—one that delivers blisteringly fast load times, phenomenal developer experience (DX), and seamless global deployments—you’ve likely heard the buzz around Astro and Cloudflare. 
---

Astro is a modern web framework that pioneered "Island Architecture," shipping zero JavaScript to the client by default. Cloudflare Pages (backed by Cloudflare Workers) is a blazing-fast hosting platform that serves your site from the edge, mere milliseconds away from your users.

Put them together, and you have a match made in heaven. Let's walk through how to set up a brand-new Astro project and deploy it to the edge using Cloudflare.

## Prerequisites
Before we launch, make sure you have:
*   **Node.js** installed (v18.14.1 or higher).
*   A package manager (npm, pnpm, or yarn).
*   A **Cloudflare account** (the free tier is incredibly generous).
*   A GitHub or GitLab account (for continuous deployment).

---

## Step 1: Scaffolding Your Astro Project

Let's get our project off the ground. Open your terminal and run the Astro setup wizard. We'll name our project `astro-cloudflare-blog`.

```bash
npm create astro@latest
```

The wizard will ask you a few questions. For this guide, you can choose the "Include sample files" option, install dependencies, and initialize a Git repository. 

Once the setup is complete, navigate into your new directory and start the local dev server:

```bash
cd astro-cloudflare-blog
npm run dev
```
Open `http://localhost:4321` in your browser. You should see the default Astro starter page!

---

## Step 2: Adding the Cloudflare Adapter

By default, Astro builds a static site (SSG). Static sites are great, but what if you want to use Server-Side Rendering (SSR) for dynamic API routes, authentication, or personalized content? 

To run Astro dynamically on Cloudflare's Edge Network, we need to add the official Cloudflare adapter. Astro makes this delightfully easy. Kill your dev server (`Ctrl + C`) and run:

```bash
npx astro add cloudflare
```

Astro will automatically install the necessary dependencies (`@astrojs/cloudflare`) and update your `astro.config.mjs` file.

Your `astro.config.mjs` will now look something like this:

```javascript
import { defineConfig } from 'astro/config';
import cloudflare from '@astrojs/cloudflare';

export default defineConfig({
  output: 'server', // or 'hybrid' if you want mostly static with some dynamic routes
  adapter: cloudflare()
});
```

*Note: Setting the output to `server` means every page is rendered on request. If most of your site is static, use `hybrid` to pre-render pages by default and opt-in to SSR only where you need it.*

---

## Step 3: Testing Locally with Wrangler

Because we are now relying on Cloudflare's serverless environment, standard `npm run dev` won't emulate the edge environment perfectly. To test Cloudflare-specific features locally, we use **Wrangler**, Cloudflare's CLI.

First, build your Astro project:

```bash
npm run build
```

Then, use Wrangler to preview the built site locally:

```bash
npx wrangler pages dev ./dist
```

This spins up a local server that accurately simulates the Cloudflare Pages environment. It's the perfect way to catch edge-specific bugs before pushing to production.

---

## Step 4: Deploying to Cloudflare Pages

Deploying your site is remarkably simple using Git integration. 

1.  **Commit and Push:** Commit your code and push it to a new repository on GitHub or GitLab.
2.  **Open Cloudflare Dashboard:** Log in to your Cloudflare account and navigate to **Workers & Pages**.
3.  **Create an Application:** Click **Create application**, select the **Pages** tab, and then click **Connect to Git**.
4.  **Select Your Repo:** Authorize Cloudflare to access your GitHub/GitLab account and select your `astro-cloudflare-blog` repository.
5.  **Configure the Build:** Cloudflare is pretty smart and usually auto-detects Astro, but ensure your settings match this:
    *   **Framework preset:** Astro
    *   **Build command:** `npm run build`
    *   **Build output directory:** `dist`

Click **Save and Deploy**. Cloudflare will pull your code, build it, and deploy it to their global edge network in seconds. 

## The Wrap Up

And just like that, you’ve built an SSR-capable Astro application and deployed it to the edge! 

Every time you push a new commit to your `main` branch, Cloudflare will automatically build and deploy the changes. You now have a platform capable of handling intense traffic with almost zero latency, while maintaining a codebase that is a joy to work in.

**What's next?**
*   Explore Astro's UI integrations (React, Vue, Svelte) to sprinkle interactive "islands" onto your pages.
*   Check out Cloudflare D1 (serverless SQL) or KV (key-value storage) and connect them to your Astro server routes for full-stack edge computing.

Happy coding! 