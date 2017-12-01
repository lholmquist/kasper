---
layout: post
title:  "Chrome App Push Update"
date:   2014-08-28
permalink: chrome-app-push-update
categories: 
---

So recently Chrome apps added the ability to use the same GCM for Android as, ummm, Android for receiving Push Notificaitons.

[Chrome gcm API](https://developer.chrome.com/apps/gcm)

This is really cool and much easier to use than the previous "pushMessaging" api that came before it. _which will become legacy soon_

[Chrome pushMessaging API](https://developer.chrome.com/apps/pushMessaging)

### The Problem?

However; there is one problem.

The current "Chrome" implementation on the [AeroGear UnifiedPush Server](https://github.com/aerogear/aerogear-unifiedpush-server/releases/tag/1.0.0.Final) uses the old API.

But guess what, you can still use this new API today!!  Thats right folks,  just use the Android Variant!!

There is work being done to update the UI/Examples so you as a user will won't have to use the Konami code to set up and send Push Notifications to your Chrome App.


If you want to learn more about the new GCM api for chrome apps,  [go here](https://developer.chrome.com/apps/gcm) (yup,  same link as earlier)


And as always,  a lolcat


![](http://whall.org/blog/files/lolcats-enthralling.jpg)




