---
layout: post
title: My thoughts on - StatefulViewController
tags: iOS
comments: true
author: eliasz
---
I had a chance to play a little bit with a nice pod that is called StatefulViewController, created by Alexander Schuch. StatefulViewController works with both `UIView` and `UIViewController`, and allows you to introduce placeholders for their different states: Loading, Error, Empty or Content. Everything is based on an intuitive protocol and after providing your placeholder views and implementing required methods everything works like a charm. You can find this pod on Alexander's [github](https://github.com/aschuch/StatefulViewController). I've looked into the code of this pod and I found a few things that I want to share with you today.


###StateMachine
Whole idea is based on ViewStateMachine, which handles transitions of views that you have assigned to each state. All states are represented by this nice enum
<script src="https://gist.github.com/Eluss/3b3b2b78e7b286bc6e5c.js"></script>
Notice, that this enum conforms to `Equatable` protocol, which lets us implement `"=="` operator for this enum. When you do this, standard library provides an implementation for `"!="`. This move allows us to compare states later on.
<script src="https://gist.github.com/Eluss/f0672e20fa6690f7e492.js"></script>

###Subscript
Another thing that was interesting for me was `subscript` provided for a `ViewStateMachine` class."You use subscripts to set and retrieve values by index without needing separate methods for setting and retrieval." - note from Apple.
<script src="https://gist.github.com/Eluss/8d3920fa7b3878a8f105.js"></script>

Example use: 
<script src="https://gist.github.com/Eluss/1bc3e208110fd3e86ebf.js"></script>


###Extension property 
Normally, you can't add a property to a class via an extension, but you can achive this using an associated object.
<script src="https://gist.github.com/Eluss/1586b4a35898a136bc56.js"></script>

###Documentation
I really liked the documentation that was provided with this pod. In Objective-C we had a nice header file, which was pretty easy to read from. Here we have two files. `StatefulViewController.swift` file, which provides nice documentation and `StatefulViewControllerImplementation.swift` which containts implementation of `StatefulViewController` class.

It was really interesting code, which taught me some new thing. I hope that you liked it too!


*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
