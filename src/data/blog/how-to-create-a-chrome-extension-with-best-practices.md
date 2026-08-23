---
title: How to create a Chrome extension with best practices 
author: Sunny
pubDatetime: 2026-08-14T04:06:31Z
slug: how-to-create-a-chrome-extension-with-best-practices
featured: false
draft: false
tags:
  - Chrome Extension
  - MV3
description:
  Building a Chrome Extension is essentially building a small web application using HTML, CSS, and JavaScript, but with access to Chrome's powerful browser APIs. Since 2024, Google has mandated the use of Manifest V3 (MV3), which focuses heavily on enhanced security, privacy, and performance.
---

Here is a comprehensive guide to understanding the architecture, building your first extension, and following industry best practices.

---

## The 4 Core Components

Before writing code, it is important to understand the building blocks of a Chrome extension:

1. **The Manifest (`manifest.json`):** The blueprint of your extension. It tells Chrome what files to load, what permissions you need, and metadata like version and name.
2. **Service Workers (Background Scripts):** The event handlers. They run in the background, dormant until needed, and handle browser-level events (like a new tab opening or a bookmark being created). In Manifest V3, these replaced persistent background pages to save memory.
3. **Content Scripts:** The DOM interactors. These are JavaScript files injected directly into web pages to read or modify the page content.
4. **User Interface (UI):** Your popups (`popup.html`), sidebars, or options pages where users configure settings or trigger actions.

---

## How to Build a Basic Extension (Step-by-Step)

Let's build a simple extension that changes the background color of the current webpage when clicked.

1. **Create the Project Directory:** Keep it organized.
Create a new folder on your computer named `color-changer-extension`. This will hold all your HTML, CSS, JavaScript, and image files.


2. **Define manifest.json:** The critical configuration file.
Inside your folder, create a file named `manifest.json`. This tells Chrome everything it needs to know.

```json
{
  "manifest_version": 3,
  "name": "Color Changer",
  "version": "1.0",
  "description": "Changes the background color of the current page.",
  "action": {
    "default_popup": "popup.html"
  },
  "permissions": ["activeTab", "scripting"]
}

```


3. **Create the User Interface (Popup):**
Create `popup.html`. This is the menu that drops down when a user clicks your extension icon in the toolbar.

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      body { width: 150px; padding: 10px; text-align: center; font-family: sans-serif; }
      button { padding: 8px 12px; cursor: pointer; }
    </style>
  </head>
  <body>
    <h3>Pick a Color</h3>
    <button id="changeColorBtn">Make it Blue!</button>
    <script src="popup.js"></script>
  </body>
</html>

```


4. **Write the Logic:** Connecting the UI to the page.
Create `popup.js`. This script listens for the button click and tells Chrome to execute a script on the active tab.

```javascript
document.getElementById('changeColorBtn').addEventListener('click', async () => {
  // Get the current active tab
  let [tab] = await chrome.tabs.query({ active: true, currentWindow: true });
  
  // Execute a script on that tab
  chrome.scripting.executeScript({
    target: { tabId: tab.id },
    function: setPageBackgroundColor,
  });
});

// The function that runs IN the web page
function setPageBackgroundColor() {
  document.body.style.backgroundColor = '#add8e6';
}

```


5. **Load Unpacked in Chrome:** Testing your code locally.
1. Open Google Chrome and navigate to `chrome://extensions/`.
2. Toggle **Developer mode** on (top right corner).
3. Click the **Load unpacked** button (top left).
4. Select your `color-changer-extension` folder.
5. Pin your new extension to the toolbar, click it, and test the button!


---

## Best Practices for Production Extensions

If you plan to publish your extension to the Chrome Web Store, you must adhere to Google's strict guidelines.

### 1. Security & Privacy First

* **Principle of Least Privilege:** Only request permissions you absolutely need. If your extension only alters YouTube, use `"matches": ["*://*[.youtube.com/](https://.youtube.com/)*"]` instead of requesting access to all URLs (`"<all_urls>"`). Over-requesting permissions is the #1 reason extensions get rejected by the Web Store.
* **Avoid `eval()`:** Never evaluate strings as code. It exposes your extension to Cross-Site Scripting (XSS) attacks.
* **Sanitize Inputs:** If you are taking user input and injecting it into the DOM via a content script, always sanitize it first to prevent malicious script injection.

### 2. Manifest V3 Performance

* **Embrace Ephemeral Service Workers:** MV3 service workers shut down when inactive. Do not rely on global variables to store state across events. Instead, use `chrome.storage.local` or `chrome.storage.session` to save data.
* **Targeted Content Scripts:** Inject content scripts programmatically using the `chrome.scripting` API (like in the example above) rather than injecting them passively on every single page load via the manifest.

### 3. User Experience (UX)

* **Design for Accessibility:** Ensure your popup HTML is navigable via keyboard and accessible to screen readers (use proper ARIA tags).
* **Handle Errors Gracefully:** Network requests fail, and APIs change. Always wrap your background logic in `try/catch` blocks and use `chrome.runtime.lastError` to check for silent failures.
* **Respect the Browser UI:** Keep your popups lightweight. If your extension requires heavy configuration, use an Options Page (`"options_page": "options.html"`) which opens in a full browser tab.