---
layout: post
title:  "Electron Framework"
subtitle: "Pros and Cons"
date: 2020-11-23 19:50:00 +000
categories: framework
tags: electron framework javascript
comments: true
author: "Mehran Zand"
meta: "electron framework javascript"
---
Today, I was looking for the best technology/framework for the new monitoring desktop app for the company with accessibility factor for cross-platform.

Cross-platform technologies can be really frustrating, based on my previous experiences. But in these years, the cross-platform technologies really improved.

There are several options in the market to choose. I'm going to look at the [Electron] framework briefly.

### What's the Electron.js
Electron is a platform that makes it possible for developers to build cross-platform desktop apps using web technologies like JavaScript, HTML, CSS.

Along with these tools, many JS libraries and frameworks can be used. In fact, any SPA (single-page app) framework can be used with Electron.

![Electron Framework](/assets/2020-11-22-electron-framework/electron-platform.png)

What electron basically does is bundle a browser engine ([chromium] in this case) with your app. The browser engine takes care of rendering your app.

### Main Advantages of Electron.js
I think the main advantage of electron framework for developers is a usage of **HTML**, **CSS**, **JS** to create an electron app.
Furthermore the electron apps similar to the Web Apps basically and finally the electron uses chromium engine for rendering UI. This means that you can get several benefits from this like **Developer Tools** etc.

### Disadvantages of Electron.js
Every electron app must released by its own chromium engine.
This means the chromium engine must be included for every electron app. The chromium project is really large scale (20 millions line of code approximately) it cloud be add **100 MB** to size of electron app. 
On the other hand in a large applications electron consumes a lot of system recourse such **RAM** or battery usage (on laptops). It's not really good but for the mid size application it should be doesn't matter.

### In conclusion
I still think electron is great option provided that you really need a desktop application so you can ignore some disadvantages. many of well-known application built on the electron.js framework such as **Skype**, **Slack**, **WhatsApp** and so on. Please leave a comment below, and let me know what you think.

[Electron]: https://www.electronjs.org/
[chromium]: https://www.chromium.org/