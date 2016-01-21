---
layout: post
title: ReactiveCocoa 4 - MutableProperty
tags: iOS
comments: true
author: eliasz
---

MutableProperty which comes with ReactiveCocoa allows us to track variable's changes. Let's take a quick look on how it actually works.

Let's assume, that we want to create a bank account balance variable, that we will be tracking later on. 
<script src="https://gist.github.com/Eluss/e3d97651eb2400cc6545.js"></script>

MutableProperty has three fields that we are especially interested in.

###Value
First one is `value`, which grants us access to a current value of our property. Everytime we change a this value, all observers of signals created by a producer get notified about it.

###Producer
Second field is `producer`, which is a factory for started signals which we use in order to track our property. Lets use our factory to create a signal out of our MutableProperty.
<script src="https://gist.github.com/Eluss/8e0402d073d48e42fd88.js"></script>
Everytime our property changes, it will be printed. If property is deinitialized, signals created by producer are completed.

###Observer
Our `observer` is initialized as a buffer with a capacity of 1. What does it mean? When we set our value, we also push it to an observer. Our observer has an internal buffer, which in this case remembers last pushed value. This value will be passed to observer every time producer starts a new signal.

Passing values to our buffered observer is handled internally and happens everytime a setter for a `value` is called. So in fact mostly we will be having fun with `value` and `producer` fields.



*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
