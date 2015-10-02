---
layout: post
title: Data transfer between Apple Watch and iPhone
tags: iOS
comments: true
author: eliasz
---

Today's post will cover basic data transfer between your iPhone app and Apple Watch app.
Let's assume that you have already created an Apple Watch extension in your project and you want to transfer some data to your watch.
As an example, we will be sending `Event` object to our watch, so let's have a look at `Event` class!
<script src="https://gist.github.com/Eluss/386d83ccffa658a63054.js"></script>

The most important thing here is to implement `NSCoding` protocol. We will need it, because we won't be able to send pure `Event` object to our watch app, however we can easily send `NSData`. Another important thing is to make our class visible to both iPhone and Apple Watch, so remember about linking this class to both targets.

Ok! We've got our object. Next thing we want to do is to establish our connection. In our case, we will get the data after tapping on the button displayed on the watch's screen. Here is the button action code:
<script src="https://gist.github.com/Eluss/2f0c64a042b9c3b2fd1a.js"></script>

Don't get mislead! Method `openParentApplication` will not open an app on your iPhone. It will quietly run it in the background and let you quickly do some actions. Here, we are sending a dictionary to our iPhone app, and we expect to get a dictionary back as well. Let's see the code on iPhone's side. This method is placed in our `AppDelegate`:

<script src="https://gist.github.com/Eluss/ad28c1f7d7aa85e01c17.js"></script>

We can easily get message that we previouly sent from the watch. Then we create our event, archive it and send it as `NSData` packed inside the dictionary.

It's not the only way of transfering the data, so feel encouraged to look for another solutions!

*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
