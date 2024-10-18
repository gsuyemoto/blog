---
title: Rust and WASM
slug: rust-and-wasm
date: 2023-07-27
draft: true
categories:
  - Rust
  - WASM
  - Web Dev
---

  [WASM template]: https://github.com/Mubelotix/wasm-extension-template
  [browser's extensions]: https://superuser.com/questions/247651/how-does-one-install-an-extension-for-chrome-browser-from-the-local-file-system
  [background vs. content]: https://stackoverflow.com/questions/12971869/background-scripts-vs-content-scripts-in-chrome-extensions
  [more background vs. content]: https://developer.chrome.com/docs/extensions/mv3/getstarted/extensions-101/#extension-files
  [Firefox background]: https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/background
  [debugging Chrome extensions]: https://developer.chrome.com/docs/extensions/mv3/tut_debugging/

# Rust and WASM
This is definitely an article to help me as a constant reminder of the Rust and WASM process. Every time I revisit using WASM with Rust, I seem to run into some problem. In one of my project folders, I had a WASM that I know was working a month ago...

<!-- more -->

And now, running it today, I get an error. There is definitely a dearth of information on the topic of developing WASM targets using Rust and this is especially so when targeting browser extensions.

!!! warning
    Using wasm-pack like the following resulted in several errors:
    ````bash
    wasm-pack build --target web
    ````

![Error 1](../assets/rust-and-wasm-error01.jpg)
![Error 2](../assets/rust-and-wasm-error02.jpg)

After looking around and not really finding any good information as to the reason for this error, least of all how to resolve it, I decided to try something else. Previously, I had tried using a [WASM template] in order to generate a WASM/Rust project folder. Going back to this folder, I compiled it and then imported the resulting folder into the [browser's extensions] and it worked.

Ok, so, after a bit more trial and error, I found out what I was doing wrong the with the first WASM project folder.
````bash title="Instead of using..."
wasm-pack build --target web
````
````bash title="Use this instead..."
wasm-pack build --target no-modules
````

Here are a couple of confusing parts of WASM (and extension dev in general):
* What is the difference between [background vs. content] scripts? Content and background scripts have different permissions and access to different browser APIs. Content scripts have full access to the DOM, but limited access to browser/extension specific APIs. Background scripts have full access to browser APIs, but don't have access to the DOM. Here is [more background vs. content] information.
* What is the difference between chrome.action and chrome.browserAction? They both have access to the same APIs, but one is suited for buttons that are enabled all the time whereas the other is suited for a button that is active for specific tabs/urls.

Some gotchas I found recently:
* When creating a background script, the script would need to have a "ready" function at the beginning of the script or else many APIs when accessed immediately will not be in scope in time. Thus, an error will be thrown immediately upon the background script loading. Using "(async () => {" at the top of the script fixed the issue.
* Console.log does not seem to be working in background script.
* Chrome and Firefox handle the browser top most object differently. In Chrome, you call the top level object with "chrome," and with Firefox it's "browser".
* Firefox and Chrome handle the key for background scripts in the manifest differently. In Firefox, there are no service workers and instead use "scripts" to denote background scripts to import, see [Firefox background]. In Chrome, the manifest key is "service_worker".
* When using chrome.action, make sure that there is a corresponding entry in the manifest (at least a manifest key with "action" and maybe a minimal entry of "default_title"). With Chrome, "action" is the appropriate call as opposed to the similary "browserAction" which seems to be deprecated for manifest v3.
* When debugging with console.log you will find that the log output will only show when it's used in the content scripts context. When attempting to use the console.log in a background or popup script, you can only view the corresponding log entry when inspecting popup (right click on the popup and click "inspect") or in the case of the background script you can find the extension ID and go to chrome-extension://extension-id/manifest.json. For more info, go to this link for [debugging Chrome extensions].
* You can debug with alert messages via background and popup scripts as the alert is not part of the DOM which you do not have access to with the background and popup scripts.
* You need the tab id when injecting content scripts from a background script (or script of similar context) after manifest V3 as the getCurrentTab.executeScript is deprecated. And, in order to get the tab id, I found the best way is to use the 'tab.query' for the 'active: true' tab to be the best way to do so.
* When injecting a content script that will call the wasm object, you will probably need to use the 'files' key and pass an array of scripts which includes the wasm scaffolding script created with wasm-bindgen or wasm-pack:
````javascript
try {
  await chrome.scripting.executeScript({
    target: {
      tabId: tabID,
    },
    files: ["wasm_scaffold.js","run_wasm.js"]
  })
}
````
Whereas the run_wasm.js is a simple script like:
````javascript
const runtime = chrome.runtime || browser.runtime;

async function run() {
  await wasm_bindgen(runtime.getURL('wasm_bg.wasm'));
  // if the rust wasm code does not include a #[wasm_bindgen(start)] then you will need to call a function like:
  // wasm_bindgen.start();
}

run();
````

