---
layout: post
title: My thoughts on - Whisper
tags: iOS
comments: true
author: eliasz
---

Lately I've come across a nice pod which makes in-app messaging easier and decided to give it a quick look. I've created a test project in order to see how the pod behaves and my first impression was... "That was easy!". If you want to find out how to use this pod, check out Hyperoslo's [github](https://github.com/hyperoslo/Whisper), which shows how easy it is to start sending your messages!

I took a quick look into the source code of this pod and instantly found two things that caught my eye.
####Creating UI components
First thing that was interesting for me, was the way UI components were initialized. For me, it's normal to set everything up when I create an instance of my `UIView`. You can see an example of this in my test project, where in `MainView` class I create buttons in `setupView` method. In Whisper's source code I found this:
<script src="https://gist.github.com/Eluss/0508b7d106b94f2a7946.js"></script>
It's an example of lazy initialization that can be used for UI components and some other variables. The private(set) is also great, as you can access the variable from outside without the need of providing additional getter methods.

####Iterating
Second interesting thing was the use of sequence type methods for iterating:
<script src="https://gist.github.com/Eluss/d05268f238222b36b014.js"></script>

I'm used to the method below, because I like the readability of it, but the `forEach` method looks nice as well. 
<script src="https://gist.github.com/Eluss/5049b488041e6196a668.js"></script>

My test project is  available on my [github](https://github.com/Eluss/WhisperPodTest.git) if you want to give it a look.

*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
