---
layout: post
title: Creating an UIImage using ImageIdentifiers 
tags : [iOS, swift]
comments : true
author: kwysocki
---

Hi! In my first post I want to share with you some cool tip which I watched in 'Swift in Practice - Apple WWDC 2015' Alex Migicovsky presentation about using UIImages in Swift project. 

Normally you create UIImageView with UIImage like this : 

<script src="https://gist.github.com/k8mil/6d28046f02802b811780668680f84a80.js"></script>

or like this 

<script src="https://gist.github.com/k8mil/5750d3c2a9a272b2d7c32baaffb3470b.js"></script>

But what happens If you have typos (e.g. "rign") in file name or there is not an image named "ring" ?
Nothing. You don't get any warnings or errors from a compiler. Everything will compile without problems. But unfortunately our UIImageView will be empty and definitely you don't want it.

Also let's say you're working on a big project and you use UIImages in many places, then you discover you have a typo in an image name. Of course you don't get any warning or error and you have to go through the whole project and find all usages of this image and possibly fix it.  

Now, let's try to deal with this problem...
---

1.Create special Enum for all UIImages used in project. 

<script src="https://gist.github.com/k8mil/5854408e1f50790deb8cc720b0b64720.js"></script>

2.Create convenience initializer for UIImage

<script src="https://gist.github.com/k8mil/f09177294695cfc9e9fd3facc857157e.js"></script>

3.Use it!
		
<script src="https://gist.github.com/k8mil/da1c87cede7eba3f11ec7afc7d8523a9.js"></script>

Some of you can say "Ok, ok... but what if I have typos in some ImageIdentifier value ?" That's the point. Best advantage of this solution is that you have only one place where all images are placed and it is only place where you can fix it. You don't have to search whole project and find usages of the image.

Another advantage is that you will get autocomplete on UIImage constructor, and choosing proper image will be easiest. 

What about...

<script src="https://gist.github.com/k8mil/869d0e15ae8e730a054d8a04735c4f9e.js"></script>

As you can see I have a typo in name of enum value, but now I will get a compiler error saying : 

`(...)type 'ImageIdentifier' has no member 'GoldRign'`

So compiler can help you with creating UIImage !

In essence, I hope all of you want to write a safe and clean code. You'll get a cleaner code because you have one place in your code where all images are defined and you’ll get a safer code because a compiler or IDE can help you while creating the UIImage.


Small Update
---
Previously swift did not support optional initializers for UIImage and above lines of code looked like this

<script src="https://gist.github.com/k8mil/e88cbeabd058cb618d7d6df1e19a05ad.js"></script>

Now swift added the concept of failable initializers and the UIImage initializer returns an optional so if UIImage cannot be created it will be nil. 
Maybe now this concept lose some value because you don't have to care about unwrapping image but I truly recommend you to use this UIImage concept in your projects.

<br>
Hope that help you!
<br>
*This article is cross-posted with my [my personal blog](http://k8mil.github.io/).*