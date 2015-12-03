---
layout: post
title: Quickly access Documents Directory for the iOS Simulator
tags: iOS
comments: true
author: michal
---

Let me share a small trick that I use to quickly navigate to documents directory for an iOS application that runs in the simulator.
 It requires adding some small snippet of code to the app but it really pays off.

Here's the snippet:
<script src="https://gist.github.com/mgamer/63207d324306dec8a056.js"></script>

In essence it creates a symbolic link to app documents directory and puts that symbolic link on your Desktop. Just add it to `application:  didFinishLaunchingWithOptions:` method and you will find `SimulatorDocuments` link recreated on your Desktop anytime you run the app in the simulator.




