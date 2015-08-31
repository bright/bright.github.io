---
layout: post
title: Queueing audio files with AVQueuePlayer
tags: iOS
comments: true
author: eliasz
---

Today's short post will cover queueing audio files using Swift. In order to do this we will be using AVQueuePlayer.

####Import AVFoundation in order to use the AVQueuePlayer
<script src="https://gist.github.com/Eluss/fcb88bf8c43ab1033104.js"></script>

####Create an instance of AVQueuePlayer.
<script src="https://gist.github.com/Eluss/f7eb42d773a2d8773200.js"></script>
Ps. Hold it as instance variable. If you create it as local variable, you will lose it after exiting the scope and audio won't play.

####Add audio file to queue.
Here we will be adding a file which is added to our project. In order to do this, we create an instance of AVPlayerItem and add it to an existing queue. Passing nil as second argument of insertItem function will insert it at the end of the queue.
<script src="https://gist.github.com/Eluss/352e66eb8cacfde429e8.js"></script>

####Play/pause/skip.
<script src="https://gist.github.com/Eluss/538b5cebd23e7659187b.js"></script>

Tip: Once you call a play method, you can keep adding the files to queue and they will automatically start.

*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
