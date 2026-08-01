---
title: Get started with playwright how to create a proper scraper with this parse html
author: Sunny
pubDatetime: 2026-07-31T04:06:31Z
slug: get-started-with-playwright
featured: false
draft: false
tags:
  - SRE
  - Devops
description:
  Imagine opening your favorite streaming platform during a major sporting event and finding it unavailable Or attempting an online payment only to encounter a server error Or searching the web and receiving no results.
---
This is a great time to start with Playwright for web scraping. Heading into 2026, Playwright has essentially taken over the browser automation space—outpacing Puppeteer and Selenium, and becoming the default engine for major AI scraping agents.

Because of its auto-waiting features, multi-browser support, and ability to handle modern JavaScript-heavy, single-page applications, it is the perfect tool for data extraction.

Here is a guide to getting started with Playwright, written for TypeScript, but the concepts apply identically to Python.

---

# Getting Started With Playwright Scraping in 2026

If you're dealing with modern websites that load content dynamically via XHR/Fetch, infinite scrolling, or complex React/Angular states, basic HTTP libraries like `axios` paired with `cheerio` won't cut it. You need a real browser.

Playwright drives Chromium, WebKit, and Firefox directly. Here’s how to build a robust scraper that won't break the moment the site changes a CSS class.

## 1. Setup and Installation

First, initialize a Node.js project and install Playwright.

```bash
npm init -y
npm install playwright

```

Playwright comes with its own browser binaries, ensuring that the version of Chromium you automate perfectly matches the Playwright API version you are using.

## 2. Your First Scraper

Let’s write a scraper that goes to Hacker News, waits for the content to load, and extracts the top article titles.

```typescript
const { chromium } = require('playwright');

(async () => {
  // Launch Chromium in headless mode
  const browser = await chromium.launch({ headless: true });
  
  // Create a new browser context (think of it as a fresh incognito window)
  const context = await browser.newContext();
  const page = await context.newPage();

  // Navigate to the target site
  // waituntil: 'domcontentloaded' is faster than 'load' and perfect for scraping
  await page.goto('https://news.ycombinator.com/', { waitUntil: 'domcontentloaded' });

  // Use page.$$eval to run JavaScript in the browser context
  // and map the matched elements to an array of text strings
  const titles = await page.$$eval('.titleline > a', (elements) => 
    elements.map((el) => el.textContent.trim())
  );

  console.log('Top Articles:', titles);

  // Always close the browser when done to free up memory
  await browser.close();
})();

```

## 3. Best Practices for Locators and Parsing

The biggest mistake developers make when scraping with Playwright is treating it like legacy `cheerio` parsing and using brittle CSS selectors. If a designer changes `.article-title-v2` to `.card-heading`, your scraper breaks.

### The Locator Hierarchy

In 2026, the industry standard for Playwright is to use **user-facing locators** rather than raw CSS or XPath selectors wherever possible. They auto-wait for the element to appear and are far more resilient to code changes.

Walk down this hierarchy when trying to target an element to scrape:

1. **`page.getByRole()`** - The gold standard. Selects elements based on their ARIA role and visible text.
*Example:* `await page.getByRole('button', { name: 'Load More' }).click();`
2. **`page.getByText()`** - Finds elements containing specific text content. Highly resilient.
*Example:* `const price = await page.getByText('$').textContent();`
3. **`page.locator()` with CSS** - Use this when you are extracting bulk lists of items where roles and text aren't viable. Avoid XPath entirely; it is brittle and slow.

### Handling Dynamic Content (Auto-Waiting)

Playwright's superpower is **auto-waiting**. Before performing an action (like `.click()` or `.textContent()`), Playwright automatically waits for the element to be attached to the DOM, visible, stable (not animating), and actionable.

However, when scraping, you often need to wait for a *network* event rather than just a DOM event. If a page loads an empty shell and then fetches the data via API, you should intercept that network request rather than scraping the DOM.

```typescript
// Wait for a specific API response to finish before scraping
const responsePromise = page.waitForResponse('**/api/v1/products**');
await page.goto('https://example.com/products');
const response = await responsePromise;

// You can parse the JSON directly from the intercepted network request!
// This is much cleaner and faster than parsing the HTML.
const data = await response.json(); 

```

## 4. Bypassing Basic Blocks

If you are scraping heavily, you will eventually hit bot protection (like Cloudflare). To make your Playwright scraper look more human:

1. **Keep Session State:** Save cookies and local storage between runs so you look like a returning user rather than a fresh bot every time.
2. **Proxy Rotation:** Pass proxy credentials directly into the `browser.newContext()` to rotate your IPs.
3. **Use Stealth Plugins:** Look into tools like `playwright-extra` and `puppeteer-extra-plugin-stealth` to strip away headless browser signatures that trigger captchas.

---
When you run `npm install playwright` in your project, it only installs the Node.js API library. It does not actually download the web browsers.

That is exactly what `npx playwright install` is for. It reaches out and downloads the specific, perfectly-matched binaries of Chromium, Firefox, and WebKit that your version of the Playwright library needs to function.

Here is how to use it based on what you are trying to do.

## 1. The Standard Setup

If you are setting this up on your local development machine (Mac or Windows) for the first time, open your terminal in your project folder and run:

```bash
npx playwright install

```

This will download all three browser engines (Chromium, Firefox, WebKit). It usually takes a minute or two and requires a few hundred megabytes of space.

## 2. The Scraper Setup (Save Time & Space)

If you are only building a scraper, you almost never need Firefox or WebKit. Chromium handles 99% of scraping tasks perfectly. To save disk space and download time, tell Playwright to only install Chromium:

```bash
npx playwright install chromium

```

You can then launch it in your code using `const { chromium } = require('playwright');`.

## 3. The Server / Linux Setup (Crucial)

If you are deploying your scraper to a Linux server (like an AWS EC2 instance, a DigitalOcean droplet, or a VPS), running the standard install command usually isn't enough.

Linux servers are often missing the core system fonts and C++ libraries that browsers need to render pages. If you forget this, your script will crash with a "missing shared library" error. To fix this, append the `--with-deps` flag:

```bash
npx playwright install chromium --with-deps

```

This commands Playwright to install Chromium *and* ask the OS package manager (like `apt`) to install all the missing underlying system dependencies. (Note: This may require `sudo` privileges on your server).

---

> **Key insight:** Playwright ties the browser binary versions directly to the NPM package version. If you ever update Playwright in your `package.json` (e.g., `npm update playwright`), you **must** run `npx playwright install` again to fetch the newly matched browser versions.