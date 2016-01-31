---
layout: post
title: Heartbeat button
tags: iOS
comments: true
author: eliasz
---

Hi! Today I will show you how to create a simple heart button that will simulate a heartbeat upon each tap. You can find an example project on my [Github](https://github.com/Eluss/HeartbeatDemo.git).

##Create a button
First of all we have to create a button. We create a heart image from the resources that you can find in an example project and we assign it to our button. Then we simply position out button inside a view and assign a certain size to it. The size should equal our heart image's size. Next, we add target to our button's touchUpInside event. We will handle it in `performHeartBeat:` method.
<script src="https://gist.github.com/Eluss/55b316c52c5a054141a7.js"></script>

##Button's action
Let's take a look at our `performHeartBeat:` method. In order to perform our heart beat, we create an imageView from a `heart_border` image. ImageView's initial frame should be smaller or the same as our button's frame. If it is bigger, then the overall effect will look bad. Next we take care of our animation. In order to do that we use UIView's `animateWithDuration:` method. In our animation's body we make our `imageView` bigger by putting a `CGAffineTransformMakeScale(3,3)` as it's transform property. We also want it to fade away. We achieve it by changing it's `alpha` value to 0. In our completion block we remove our imageView from superview, because we don't need it anymore.

<script src="https://gist.github.com/Eluss/4e9734142e61bf038548.js"></script>

Here is how our button should look before tapping:

![Inactive button](/images/heartbeat-inactive.png)

After multiple taps:

![Active button](/images/heartbeat-active.png)

*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
