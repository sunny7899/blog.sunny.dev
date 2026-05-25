---
title:  Fixing Broken Form Inputs in Angular + SwiftUI WebViews
author: Sunny
pubDatetime: 2026-05-11T04:06:31Z
slug: fixing-broken-form-inputs-in-angular-swiftui-webviews
featured: false
draft: false
tags:
  - Swiftui
  - Angular
description:
  Hybrid mobile development is supposed to be a dream. You write your feature once in a robust framework like Angular, wrap it up inside a native SwiftUI iOS app using a `WKWebView`, and boom—you have a mobile app.
---

But then reality hits. You launch your app on an iOS device, go to type in a text or number field, and... *nothing*. The input box is frozen, it refuses to focus, or the keyboard completely ghosts you.

If your Angular input controls are failing inside a SwiftUI app, don't panic. The fix usually comes down to a handful of known conflicts between web styling, native Apple security, and SwiftUI layout behaviors. Here is the ultimate guide to diagnosing and fixing the problem.

Fix the iOS WebView issue where input fields get hidden behind the keyboard. This is a very common problem with WKWebView in iOS — when the keyboard appears, it doesn't automatically scroll the focused input into view. Here are the fixes:

---

## 1. The CSS Trap: `-webkit-user-select: none`

To make a web app feel like a native mobile app, developers almost always add CSS to prevent users from highlighting text when they tap around the screen.

```css
/* The hidden villain of hybrid apps */
body {
  -webkit-user-select: none; 
}

```

**Why it breaks iOS:**
In a desktop browser or Android WebView, this works fine. But in iOS `WKWebView`, applying `user-select: none` to a parent element accidentally disables the ability to tap and focus on `<input>` and `<textarea>` fields entirely.

**The Fix:**
You must explicitly override this rule for your form elements in your Angular project's global CSS file (`styles.scss` or `styles.css`).

```css
input, textarea, select {
  -webkit-user-select: auto !important;
  user-select: auto !important;
  pointer-events: auto !important;
}

```

---

## 2. The SwiftUI Hijack: `ScrollView` and Gestures

If your CSS is perfectly fine but your inputs still feel dead to the touch, look at your native iOS layout code.

**Why it breaks iOS:**
If you wrap your custom `UIViewRepresentable` WebView inside a SwiftUI `ScrollView`, `List`, or apply a generic `.onTapGesture` to its container view, **SwiftUI steals the touch events.** The native wrapper swallows the tap before it can ever pass down into the Angular DOM to focus the input box.

```swift
// ❌ DO NOT DO THIS
ScrollView {
    AngularWebView(url: appUrl) 
}

```

**The Fix:**
`WKWebView` natively handles its own scrolling. Let it do its job. Ensure your WebView sits inside a clean container like a `VStack` or `ZStack` without competing native gestures.

```swift
// ✅ DO THIS INSTEAD
VStack {
    AngularWebView(url: appUrl)
}

```

Proper SwiftUI WKWebView Setup: To ensure your WebView is configured properly to handle interactions, make sure your UIViewRepresentable struct looks similar to this baseline. It ensures JavaScript is enabled and the view is allowed to accept interactions.

```Swift
import SwiftUI
import WebKit

struct AngularWebView: UIViewRepresentable {
    let url: URL

    func makeUIView(context: Context) -> WKWebView {
        let preferences = WKWebpagePreferences()
        preferences.allowsContentJavaScript = true
        
        let config = WKWebViewConfiguration()
        config.defaultWebpagePreferences = preferences
        
        let webView = WKWebView(frame: .zero, configuration: config)
        
        // Ensure the webview itself allows interaction
        webView.isUserInteractionEnabled = true 
        webView.scrollView.isScrollEnabled = true 
        
        return webView
    }

    func updateUIView(_ uiView: WKWebView, context: Context) {
        let request = URLRequest(url: url)
        uiView.load(request)
    }
}
```
---

## 3. The 16px Auto-Zoom Annoyance

Sometimes the input box *does* work, but the moment you tap it, the entire screen violently zooms in, throwing off your beautiful Angular responsive layout.

**Why it breaks iOS:**
Apple wants to ensure readability. If a text or number input has a CSS font-size smaller than `16px`, iOS Safari/WebView automatically forces a zoom-in behavior when the keyboard opens.

**The Fix:**
Force your inputs to be at least `16px` high in your Angular styles.

```css
input[type="text"], 
input[type="number"], 
textarea {
  font-size: 16px !important;
}

```

---

## 4. Programmatic Focus is Ghosted by Apple

If you are using Angular's renderer or a custom directive to automatically focus an input box (e.g., `elementRef.nativeElement.focus()`) on page load, you might notice that the cursor blinks, but **the keyboard refuses to show up.**

**Why it breaks iOS:**
For security and UX reasons, iOS blocks the software keyboard from opening programmatically unless the focus event was explicitly triggered by a *direct physical tap* from the user.

**The Fix:**
Avoid trying to auto-focus inputs on view initialization inside iOS wrappers. If you must trigger focus via a button click, ensure the `.focus()` JavaScript command is executed immediately within the click event loop, without any asynchronous delays (like `setTimeout`).

---

## 5. Number Inputs: Swap `type="number"` for `inputmode`

Using `<input type="number">` can behave strangely in iOS WebViews, sometimes throwing native validation errors or displaying a suboptimal keyboard.

**The Fix:**
The modern, reliable approach for hybrid apps is to keep the input as `type="text"`, but leverage HTML5 `inputmode` to tell iOS exactly which keyboard to open.

```html
<!-- The rock-solid way to get a number pad on iOS -->
<input 
  type="text" 
  inputmode="numeric" 
  pattern="[0-9]*" 
  [(ngModel)]="myNumberData">

```

## 6. Tap Delay / Ghost Clicks (Angular specific)
If you have a tap delay or the input requires a "double tap" to focus, you might have conflicting touch events. If you are using Angular Material or an older Angular version with HammerJS, touch events can get swallowed.

The Fix:
Ensure your inputs don't have conflicting (click) and (touchstart) events bound to them or their immediate parent containers. If an element wrapping the input is intercepting touch events, the input will never receive the focus command.

Try wrapping your input in a standard div without any click directives to test if Angular is intercepting the event:

```HTML
<!-- Test with a completely plain input -->
<div>
   <input type="text" placeholder="Test me">
</div>
```

Other fixes you can try it out!

## 1. Swift/SwiftUI — WKWebView Configuration

```swift
import SwiftUI
import WebKit

struct WebViewContainer: UIViewRepresentable {
    let url: URL
    
    func makeUIView(context: Context) -> WKWebView {
        let config = WKWebViewConfiguration()
        
        // ✅ Critical: allows inline media and proper input handling
        config.allowsInlineMediaPlayback = true
        config.mediaTypesRequiringUserActionForPlayback = []
        
        let webView = WKWebView(frame: .zero, configuration: config)
        webView.scrollView.keyboardDismissMode = .interactive
        
        // ✅ Prevent the webview from being resized when keyboard appears
        webView.scrollView.contentInsetAdjustmentBehavior = .never
        
        return webView
    }
    
    func updateUIView(_ uiView: WKWebView, context: Context) {
        let request = URLRequest(url: url)
        uiView.load(request)
    }
}
```

---

## 2. Handle Keyboard Avoiding in SwiftUI

```swift
import SwiftUI
import WebKit
import Combine

struct ContentView: View {
    @State private var keyboardHeight: CGFloat = 0

    var body: some View {
        WebViewContainer(url: URL(string: "https://your-angular-app.com")!)
            .padding(.bottom, keyboardHeight)
            .animation(.easeOut(duration: 0.25), value: keyboardHeight)
            .onAppear {
                observeKeyboard()
            }
    }

    private func observeKeyboard() {
        NotificationCenter.default.addObserver(
            forName: UIResponder.keyboardWillShowNotification,
            object: nil,
            queue: .main
        ) { notification in
            if let frame = notification.userInfo?[UIResponder.keyboardFrameEndUserInfoKey] as? CGRect {
                keyboardHeight = frame.height
            }
        }

        NotificationCenter.default.addObserver(
            forName: UIResponder.keyboardWillHideNotification,
            object: nil,
            queue: .main
        ) { notification in
            keyboardHeight = 0
        }
    }
}
```

---

## 3. Angular Side — JavaScript Fix (inject into WebView)

Inject this JS after your page loads to auto-scroll inputs into view:

```swift
// In your makeUIView or after page load
let js = """
    (function() {
        function scrollToFocused(el) {
            setTimeout(function() {
                el.scrollIntoView({ behavior: 'smooth', block: 'center' });
            }, 400);
        }

        document.addEventListener('focusin', function(e) {
            var el = e.target;
            if (el.tagName === 'INPUT' || el.tagName === 'TEXTAREA' || el.tagName === 'SELECT') {
                scrollToFocused(el);
            }
        }, true);
    })();
"""

webView.evaluateJavaScript(js, completionHandler: nil)
```

You can inject it via `WKUserScript` to run it on every page load:

```swift
let script = WKUserScript(
    source: js,
    injectionTime: .atDocumentEnd,
    forMainFrameOnly: true
)
config.userContentController.addUserScript(script)
```

---

## 4. Angular HTML — Meta Viewport Fix

Make sure your `index.html` has this exact viewport meta tag:

```html
<meta name="viewport" 
      content="width=device-width, initial-scale=1, maximum-scale=1, viewport-fit=cover">
```

And add this CSS to your Angular `styles.scss`:

```scss
// Prevent iOS bounce/scroll issues in WebView
html, body {
  height: 100%;
  overflow: hidden; // Let inner content scroll, not the root
  position: fixed;
  width: 100%;
}

input, textarea, select {
  font-size: 16px !important; // ✅ Prevents iOS auto-zoom on focus
  -webkit-appearance: none;
}
```

> ⚠️ **`font-size: 16px` is critical** — iOS zooms in automatically if the font size is below 16px, which causes the layout to shift and inputs to go offscreen.

---

## 5. UIKit Version (if not using SwiftUI)

```swift
class WebViewController: UIViewController {
    var webView: WKWebView!

    override func viewDidLoad() {
        super.viewDidLoad()
        setupWebView()
        observeKeyboard()
    }

    func setupWebView() {
        let config = WKWebViewConfiguration()
        webView = WKWebView(frame: view.bounds, configuration: config)
        webView.autoresizingMask = [.flexibleWidth, .flexibleHeight]
        view.addSubview(webView)
    }

    func observeKeyboard() {
        NotificationCenter.default.addObserver(
            self,
            selector: #selector(keyboardWillShow(_:)),
            name: UIResponder.keyboardWillShowNotification,
            object: nil
        )
        NotificationCenter.default.addObserver(
            self,
            selector: #selector(keyboardWillHide(_:)),
            name: UIResponder.keyboardWillHideNotification,
            object: nil
        )
    }

    @objc func keyboardWillShow(_ notification: Notification) {
        guard let keyboardFrame = notification.userInfo?[UIResponder.keyboardFrameEndUserInfoKey] as? CGRect else { return }
        let insets = UIEdgeInsets(top: 0, left: 0, bottom: keyboardFrame.height, right: 0)
        webView.scrollView.contentInset = insets
        webView.scrollView.scrollIndicatorInsets = insets
    }

    @objc func keyboardWillHide(_ notification: Notification) {
        webView.scrollView.contentInset = .zero
        webView.scrollView.scrollIndicatorInsets = .zero
    }
}
```

---

## Conclusion

| Fix | Where | Priority |
|---|---|---|
| `font-size: 16px` on inputs | Angular CSS | 🔴 Must |
| Keyboard height padding | SwiftUI/UIKit | 🔴 Must |
| `scrollIntoView` JS injection | Swift | 🟠 High |
| `contentInsetAdjustmentBehavior` | Swift | 🟠 High |
| Viewport meta tag | Angular HTML | 🟡 Medium |

The **font-size and keyboard height** fixes alone solve 90% of cases. Start there!

---

## The Ultimate Checklist

The next time your inputs lock up in an Angular + SwiftUI setup, run through this quick triage list:

* [ ] Did I override `-webkit-user-select: none` on my inputs in Angular?
* [ ] Is my SwiftUI `WKWebView` free from native `ScrollView` or gesture wrappers?
* [ ] Are all my input font sizes set to at least `16px`?
* [ ] Am I relying on a physical user tap to open the keyboard rather than programmatic code?

By keeping your native SwiftUI container lean and protecting your Angular inputs from aggressive global CSS, you'll ensure a smooth, native-feeling user experience.