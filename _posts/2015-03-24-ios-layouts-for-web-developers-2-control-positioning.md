---
layout: post
excerpt: "Continuing the series of posts that looks into the iOS world from the web developer perspective. In the second post we're discussing multiple approaches to setting up where and how the controls on iOS are displayed and how it differs from HTML document flow."
title: "iOS layouts for web developers #2 - control positioning"
modified: 2015-03-24
tags: [iOS]
comments: true
author: adam
---

In the [first part](/ios-layouts-for-web-developers-1-basic-building-blocks/) of the [**iOS layouts for web developers** series](/ios-layouts-for-web-developers/) I’ve discussed controls as the basic building blocks that comprises the layout in iOS world and how that compares to HTML. I haven’t tackled anything about how and where these controls are drawn on the screen. The matter is complex enough so that here is the separate post only about it.

Let’s start with restating the acknowledgement that my overall goal here is trying to find best analogies to the web development world, not creating an ultimate guide to how those things should generally be done in iOS. Some approaches that are natural in HTML might be considered bad practice or at least not idiomatic in iOS.

I'll start from frame-based positioning that is historically the first approach available in iOS, but no longer considered the best one. But I can’t just skip it, primarily because there are a lot of analogies to the web and also because in order to fully embrace the newer approaches one should at least understand the primitives. It’s like one can’t really do good responsive web design lacking the ability to prepare static non-responsive layout.

Where is my control?
----

In HTML, when we don’t set any sizes nor positions for the block element, it is drawn according to the [normal document flow](http://webdesign.tutsplus.com/articles/quick-tip-utilizing-normal-document-flow--webdesign-8199) - we’ll find it next to the previous element in the document structure, sized according to the size of its child elements. The thing that struck me most when I was starting with iOS is that there is no such concept there - controls just don’t show up by default, unless we tell them explicitly where should they go and what size they should take.

> normal document flow <—> N/A

> document flow alterations like `float`, `display`, `clear` CSS properties <—> N/A

In iOS, we must explicitly control position and size of each control. Traditionally, this is done using frames. Frame is a rectangle the control takes on the screen, defined with its top-left corner coordinates within the superview, width and height. We set the frame through Interface Builder, or, if we’re creating views in the code, we either use [`initWithFrame`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instm/UIView/initWithFrame:) initializer conventionally available for all the controls or set the [`frame`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/frame) property for existing control instance.

Frame-based positioning is quite directly equivalent to the absolute positioning in the web, with all its deficiencies. We lack flexibility, we have no way to adjust to different screen sizes and we always need to operate in fixed-sized coordinate plane. In the web, we strive not to use absolute positioning, unless absolutely needed (pun intended). Generally, we should try to avoid it in iOS, too. We’ll see how is that possible later in this post.

> `position: absolute` in CSS <—> the default, frame-based positioning

Second aspect the iOS control's frame is taking care of is the control’s size. The controls don’t have any default size. Unless we do something with it, every view is 0x0 in size yielding it not really useful. In the web world every element by default is sized to make room for the content within - either the text or another elements. We can customize it through CSS, but if we throw the stylesheet away, we’d still get something visible.

For some controls that have natural (intrinsic) size available, like [`UILabel`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/) or [`UIImageView`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImageView_Class/), we might mitigate the need to use that pesky frame initialization using [`sizeToFit`](http://doing-it-wrong.mikeweller.com/2012/07/youre-doing-it-wrong-2-sizing-labels.html) method that sets the width and height of the frame to the natural size of the content.

> `width` & `height` properties in CSS <—> the default, frame-based sizing

> the default, auto-sizing for elements <—> `sizeToFit` method for some controls

First take on "responsive” layouts
----

Frame-based layout is very limiting, but it was [the simplest thing that could possibly work](http://www.xprogramming.com/Practices/PracSimplest.html), especially given the fact that it was first designed for a single size of a single device - the first-generation iPhone. The need for “responsiveness”, as we like to call it in the web world, was limited to screen rotation. When iPads came, there still weren't many apps that share the same layouts between two form factors. It’s only quite recently, with the introduction of iPhone 5 that was taller than the previous ones and especially with iPhone 6 that comes with two [totally distinct screen resolutions](http://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions), there’s a real push toward having a single layout shared between screen sizes and resolutions - something that is obvious for the modern web development.

Before iOS 6 (released in 2012), the only mean of “responsiveness” was the notion of [autoresizing mask](http://www.techpaa.com/2012/05/understanding-uiview-autoresizing.html) accessible via `UIView`’s [`autoresizingMask`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/instp/UIView/autoresizingMask) property. It allows to set which dimensions or spacing attributes should be kept and which can be modified on screen size change (rotation, basically). For example, when “flexible width” is set, on rotation, the horizontal margins widths are persisted and the control width takes the rest of available space. When, in turn, “flexible left margin” is set, the width wouldn’t change and the left margin will take all the space that’s still available.

That technique is quite natural and comes automatically in the web. One way to get the flexibility alike is to use percentage dimensions in CSS. Another way, for absolutely-positioned elements, is not to set one of the dimension to make it flexible. As an example, not setting the width while setting both left and right property will result in the same “autoresizing” behavior than setting `UIViewAutoresizingFlexibleWidth` in iOS.

> percentage values in CSS <—> autoresizing mask

> absolute positioning with not all dimensions fixed <—> autoresizing mask

Auto layouts
----

The next approach to push frame-based layouts into obsolescence is [Auto Layout](http://www.informit.com/articles/article.aspx?p=2041295), introduced with iOS 6. It is very powerful, flexible and well-thought system of constraints that one may define on the dimension and position-related attributes of the view, optionally with relation to the other views. What it means is that we may for example define that the left edge of our view is to be placed at the right edge of another view with some outset given, and the top edge of our view should be centered within its superview, and so on. Generally, we create a set of linear equations to calculate views attribute values out of another attribute values and the system tries to satisfy those equations when drawing the controls on the screen. If that succeeds, we end up with a nicely adaptive layout.

This approach can be used to emulate web's relative positioning when we set the constraints on the child view according to the position and size of the chosen superview. But Auto Layout gives us even more. The constraints can be set between arbitrary attributes of arbitrary views, as long as these views have a common ancestor. We can constraint the values not only to be equal, but also to be smaller (or greater) - this allows us to achieve what we do in CSS with `max-width` property and its family. Next, we’re not limited to the parent-child relationship - we can relate siblings, for example to put one next to another, emulating CSS floating. Note also that it’s nothing unusual to set constraints on the view's bottom edge - that was quite unnatural for me as I rarely use `bottom` property in CSS. With Auto Layout we can even do tricks like sticking each edge of our view to different attribute of different views. Imagination and maths are the only limits here.

> relative positioning <—> can be emulated with Auto Layout by constraining child views to parents

> `min-height`, `min-width` and similar <—> can be emulated with Auto Layout by setting constraints with inequalities

> float-based positioning <—> can be emulated with Auto Layout by constraining sibling views

The real responsiveness?
----

While Auto Layout is a great and powerful tool that sometimes even outpaces the techniques and approaches known from the web to achieve flexibility, in some cases it was still impossible or at least cumbersome to define a single layout for all the screen form factors available, especially for both iPad and iPhone. Similarly to the web, when we’re utilizing the responsive web design principles, we’re not just scaling and rearranging the elements. More than often we differentiate what and where we show some parts of the layout using [media queries](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries) and its [breakpoints](https://developers.google.com/web/fundamentals/layouts/rwd-fundamentals/how-to-choose-breakpoints), most commonly based on the screen width.

iOS 8 introduced the concept of [Adaptive UI](http://www.imore.com/adaptive-ui-ios-8-explained) that is aimed to achieve something similar - allow reorganizing layouts for various screen sizes and orientations. Views and view controllers got a new [`traitCollection`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITraitEnvironment_Ref/index.html#//apple_ref/occ/intfp/UITraitEnvironment/traitCollection) property that exposes [`UITraitCollection`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/) instance, keeping current screen size factors, so we can code against the current values. Similar interface is exposed to work with appearances (we’ll discuss appearances next in the series, when we talk about CSS alternatives for iOS). Probably more useful part is the [`traitCollectionDidChange` event](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITraitEnvironment_Ref/index.html#//apple_ref/occ/intfm/UITraitEnvironment/traitCollectionDidChange:) triggered on the screen rotation (actually, it is called “when the iOS interface environment changes”, it will be even more useful in case Apple introduces a resizable device) - we can provide a custom behaviors like changing the view hierarchies or Auto Layout constraints there. But probably the most benefit one gets from the Adaptive UI surfaces through the [Interface Builder’s storyboards](http://www.bignerdranch.com/blog/iOS-8-demo-universal-storyboards-and-adaptive-ui/) that are now aware of the traits and allow us to have an universal storyboard that works across multiple form factors.

Unfortunately, Adaptive UI is far less flexible than CSS media queries. We have only few preset and quite abstract "size classes" available - compact vs. normal for width, compact vs. normal for height and scale factor value. It might not be sufficient precision for all the needs we could have. For example, the width is marked as “compact" for iPhone both in horizontal and vertical orientation and for iPad both dimensions are “normal” regarding of the orientation. Although customizing the breakpoints [is possible](https://iosdevweek.ly/k6qFtrd?sid=VPi2m9y), it’s a bit off the “Apple way” of doing things and certainly not as handy as using the defaults.

> CSS media queries based on screen size <—> partially covered by Adaptive UI trait collection

Next time in the series - [more about CSS equivalents in iOS (or lack of such)](/ios-layouts-for-web-developers-3-managing-appearance).
