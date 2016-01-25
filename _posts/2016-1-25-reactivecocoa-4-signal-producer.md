---
layout: post
title: ReactiveCocoa 4 - SignalProducer
tags: iOS
comments: true
author: eliasz
---

Today we will take a look at SignalProducer class which is provided with ReactiveCocoa 4.

How should we treat SignalProducer? It should be treated as representation of a operations/tasks. As an example, take a look at this method, which returns a SignalProducer instance.

<script src="https://gist.github.com/Eluss/1b632aad7eaf10d491af.js"></script>

We can use it to create a signal producer and assign it to a variable. Then when we have a producer that we can ask for signals. If we don't start an actual signal, nothing will happen.

<script src="https://gist.github.com/Eluss/9e564870ffa4f75b7ef9.js"></script>

What is important here, is the fact that if we ask producer for a signal, it comes with an observer attached to it. We don't have to be afraid that we missed any data.  Another thing is that we expect the same process every time we start a produced signal. That is why producers are so good for tasks like network requests.


*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
