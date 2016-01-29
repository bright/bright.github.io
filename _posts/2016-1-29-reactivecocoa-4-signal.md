---
layout: post
title: ReactiveCocoa 4 - Signal
tags: iOS
comments: true
author: eliasz
---

Today, let's take a look at basic aspect of ReactiveCocoa - Signal.

###What is it?
A signal is an event stream. When you create a Signal, you decide what type of values and errors are sent over it. That's different to what it used to be in ReactiveCocoa 2, where `RACSignal` did not have a value type attached to it. Generally, Signals are representation of event streams that are already in progress. Each signal may have multiple observers, that will detect events pushed inside the stream. A signal may have zero observers, but values will still flow down the stream.

###Example
Let's look at this example of a signal usage.

<script src="https://gist.github.com/Eluss/81de173bf0d6987656e9.js"></script>


###Basic transformation
If you are observing a signal, you can add transformations to values that you pick. In fact, by putting a transformation, you create a new stream with changed values. Signal that you use to create transformed signal is not changed.

<script src="https://gist.github.com/Eluss/e723290d5c47a5c047b5.js"></script>


*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*