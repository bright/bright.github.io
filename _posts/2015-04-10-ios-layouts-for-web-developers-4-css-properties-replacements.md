---
layout: post
excerpt: "In the fourth post in the iOS layouts for web developers series it's time for something more lightweight. We’ll go through various visual aspects of the controls and see how we can set it up, compared to CSS."
title: "iOS layouts for web developers #4 - CSS properties replacements"
date: 2015-04-10
tags: [iOS]
comments: true
author: adam
---

This is the fourth post in the [**iOS layouts for web developers** series](/ios-layouts-for-web-developers/). The previous ones were about [the controls](/ios-layouts-for-web-developers-1-basic-building-blocks/), [control positioning](/ios-layouts-for-web-developers-2-control-positioning/) and [managing the appearance](/ios-layouts-for-web-developers-3-managing-appearance/). This time something more lightweight, I hope. We’ll go through various visual aspects of the controls and see how we can set it up, compared to CSS. Let’s start with the basics - margin and padding.

Box model & controlling controls spacing
----

There was [a lot of confusion](http://en.wikipedia.org/wiki/Internet_Explorer_box_model_bug) in the past in the web world around the box model, i.e. whether the size of an element should be calculated including or excluding its padding and border sizes. Finally, by default, the HTML element size excludes padding and borders (unless we decide to change that using `box-sizing` CSS property). How does it work in iOS?

Paddings and margins are not useful at all within frame-based positioning - you place the content where you place it, full stop. In the constraints-based layouts (auto layout), though, paddings and margins make a lot of sense. In most cases in iOS these concepts are known as [inset and outset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIKitDataTypesReference/index.html#//apple_ref/c/tdef/UIEdgeInsets) and are actually a single value - we get the margin (outset) effect when we use negative value, padding (inset) for positive.

> CSS padding <—> inset, -outset

> CSS margin <—> outset, -inset

In the context of auto layout, most of the controls has some natural margins (insets) set that are used automatically whenever we’re constraining to the control’s leading space (contrary to the edge). From iOS 8, new `UIView` property was introduced to control the inset/outset value - [`layoutMargins`](http://coding.tabasoft.it/ios/ios8-layout-margins/). It allows to control the inset (or outset, if value is negative) for each side of the control separately.

> CSS padding, margin <—> `UIView`’s `layoutMargins` property

Next thing in the box model is the border. There is no such property directly available on `UIView` (except for `UITextField`’s [`borderStyle`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextField_Class/#//apple_ref/occ/instp/UITextField/borderStyle) property). In order to draw a border around the view, we need to rely on the lower level `CALayer` and use its [`borderWidth`](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CALayer_class/#//apple_ref/occ/instp/CALayer/borderWidth) and [`borderColor`](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CALayer_class/#//apple_ref/occ/instp/CALayer/borderColor) properties.

> CSS `border-color` <—> `layer.borderColor`

> CSS `border-width` <—> `layer.borderWidth`

So, how about the box model? In iOS, the size comes from the frame property, either assigned or calculated with constraints, regardless of the inset (`layoutMargins`) values or border size. Both margins and borders are drawn within a frame, so the box model on iOS actually is the same like on IE6, not like in the web nowadays.

> `box-sizing: border-box` <—> the default box model in iOS

> the web’s default `box-sizing: content-box` <—> N/A in iOS

There is one more thing specific to iOS layouts and controls sizing that should be mentioned here. Apart from the frame, the view has the sibling [`bounds`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/instp/UIView/bounds) property. While frame is about the control’s size and location within the superview’s coordinate system, [bounds are the values within the control’s own system](http://stackoverflow.com/a/11282765/142827). It means that in most cases the control’s origin is (0,0), but it changes with rotation, scaling or translations. It makes sense to use the `frame` property from the context of superview when dealing with its children (when our code runs from the top - like from the view controller - looking down in the view hierarchy). On the other hand, it makes sense to use `bounds` when dealing with the control’s own view (for example when we’re writing the custom control), as bounds are decoupled from the specifics about where and how the view is rendered at the given point of time.

Other positioning aspects
----

**Overflow** is used in the web to control what happens when the content in the element is larger than the element’s expected dimensions. When no dimensions is set, the elements grow with the content, so no overflow applied. Otherwise, [one of the tree options](https://css-tricks.com/the-css-overflow-property/) applies - either the excess content is visible outside the element’s dimensions (`overflow: visible`, the default), cut off and invisible (`overflow: hidden`) or the scrollbars are added to the side of a control to enable scrolling independent from the main window (`overflow: scroll` and `overflow: auto`). The two first options are represented directly in iOS with [`clipsToBounds` property of `UIView`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/clipsToBounds) - when set to `YES`, the content is clipped (cut off), the default is `NO`, like in the web. There is no direct replacement for automatic scrolls - in case we need that, we need to take care of it manually, creating an additional [`UIScrollView`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIScrollView_Class/).

> `overflow: visible` <—> `clipsToBounds = NO`

> `overflow: hidden` <—> `clipsToBounds = YES`

> `overflow: scroll` and `overflow: auto` <—> `UIScrollView`

**Z-Index**. The position of a control on the Z-axis is quite important when we use the absolute positioning (or frame-based positioning in iOS), as controls may arbitrarily cover and be covered by other controls and relying on the order of setting up the control is too brittle to be trusted. That’s why we use `z-index` value in CSS. In iOS, we have the same thing hidden in the `CALayer` as [`zPosition`](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CALayer_class/#//apple_ref/occ/instp/CALayer/zPosition). To avoid the need to go to that level and deal with the fact that view hierarchy is actually [not the same as layers hierarchy](http://stackoverflow.com/questions/5466116/layer-zposition-does-not-work-with-non-sibling-uiviews), we should be using `[superview bringSubviewToFront:subview]` and `[superview:sendSubviewToBack:subview]` to achieve the same in imperative, but more powerful manner.

> `z-index` CSS property <—> `layer.zPosition`, exposed through `bringSubviewToFront` and `sendSubviewToBack`

Most of the **display** CSS property, as known in the web, doesn’t have a lot of sense in iOS layouts, as there is no distinction between inline and block elements and there’s no document flow, as we’ve already learned. The only `display` value that makes any sense for iOS is `none` that allows to hide the element without actually removing it from the view. The `UIView`’s property for this purpose is conveniently called [`hidden`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/hidden).

> `display: none` <—> `hidden`

Backgrounds
----

In iOS, there is concise [`backgroundColor`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/backgroundColor) property that encapsulates most of background styling, much like `background` in CSS. At a first glance, it seems to be simple and intended for setting plain-colored backgrounds. But it’s actually much more powerful than that. `UIColor` class can be initialized using [`colorWithPatternImage` method](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIColor_Class/#//apple_ref/occ/clm/UIColor/colorWithPatternImage:) that takes an `UIImage` instance and the “color” it creates is actually an image that can fill our view’s background with repetition - however strange it might sound. In case we need more control over stretching, repetition etc., we need to defer to “manual” way. We need to add `UIImageView` as a subview to our view and ensure it is displayed at the bottom on Z-axis.

> `background-color` CSS property <—> `backgroundColor` with plain color

> `background-image` CSS property with `background-repeat: repeat` <—> `backgroundColor` with `UIColor` created through `colorWithPatternImage`

> `background-image` CSS property with stretching or custom positioning <—> `UIImageView` subview

Moreover, for a reason unknown to me, `UITextField` has [`background` property](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextField_Class/#//apple_ref/occ/instp/UITextField/background) specialized for setting and stretching the background image for a text field, so there’s no need to fall back to adding a subview in this case.

> `background-image` CSS property with stretching on &lt;input&gt; <—> `background` on `UITextField`

Text properties
----

As we discussed in [the first post](/ios-layouts-for-web-developers-1-basic-building-blocks/) the textual content in iOS may appear only within specialized controls like [`UILabel`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/) or controls supporting [`NSAttributedString`](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSAttributedString_Class/) textual values.

`NSAttributesString` has [plenty of options](https://developer.apple.com/library/ios/documentation/UIKit/Reference/NSAttributedString_UIKit_Additions/index.html#//apple_ref/doc/constant_group/Character_Attributes) for customizing text appearance, ranging from colors, through underlines and strikethroughs, to effects such as strokes and shadows. Some of the properties are encapsulated within [`NSParagraphStyle`](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/ApplicationKit/Classes/NSParagraphStyle_Class/index.html) available under `NSParagraphStyleAttributeName` key in the attributed string.

> `font` CSS properties combined <—> `NSAttributedString` with `NSFontAttributeName`

> `text-align` CSS property <—> `alignment` property of `NSParagraphStyleAttributeName` value in `NSAttributedString`

> `text-indent` CSS property <—> `firstLineHeadIntent` property of `NSParagraphStyleAttributeName` value in `NSAttributedString`

> `padding-left` CSS property <—> `headIndent` property of `NSParagraphStyleAttributeName` value in `NSAttributedString`

> `padding-right` CSS property <—> `tailIndent` property of `NSParagraphStyleAttributeName` value in `NSAttributedString`

> `line-height` CSS property <—> `lineSpacing` property of `NSParagraphStyleAttributeName` value in `NSAttributedString`

> `word-wrap` CSS property <—> `lineBreakMode` property of `NSParagraphStyleAttributeName` value in `NSAttributedString`

> `color` CSS property <—> `NSAttributedString` with `NSForegroundColorAttributeName`

> `background-color` CSS property <—> `NSAttributedString` with `NSBackgroundColorAttributeName`

> `text-decoration: line-through` CSS value <—> `NSAttributedString` with `NSStrikethroughStyleAttributeName` set to `NSUnderlineStyleSingle`

> `text-decoration: underline` CSS value <—> `NSAttributedString` with `NSUnderlineStyleAttributeName` set to `NSUnderlineStyleSingle`

> `text-stroke` CSS property ([not widely supported yet](http://caniuse.com/#feat=text-stroke)) <—> `NSAttributedString` with `NSStrokeColorAttributeName` and `NSStrokeWidthAttributeName`

> `text-shadow` CSS property <—> `NSAttributedString` with `NSShadowAttributeName`

`UILabel`, the control dedicated for text display, supports [`attributedText`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/#//apple_ref/occ/instp/UILabel/attributedText), so can handle all of the features for `NSAttributedString`, but additionally exposes some properties that affect the whole label content, like [`textAlignment`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/#//apple_ref/occ/instp/UILabel/textAlignment).

> `font` CSS properties combined <—> `UILabel`’s [`font`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/#//apple_ref/occ/instp/UILabel/font)

> `color` CSS property <—> `UILabel`’s [`textColor`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/#//apple_ref/occ/instp/UILabel/textColor)

> `text-align` CSS property <—> `UILabel`’s [`textAlignment`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/#//apple_ref/occ/instp/UILabel/textAlignment)

> `word-wrap` CSS property <—> `UILabel`’s [`lineBreakMode`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/#//apple_ref/occ/instp/UILabel/lineBreakMode)

Fancy effects
----

Some of the “fancy” text effects like text shadows or strokes are available through `NSAttributedString` we’ve just gone through. There are few more available, though. I think “fancy effects" is not a perfect heading, anyway, but I needed a place to describe few more properties that are available in iOS and are well-known in CSS but I couldn’t fit it anywhere else. So here we go:

> `opacity` CSS property <—> [`alpha`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instp/UIView/alpha)

> `animation` CSS properties family <—> whole bunch of `UIView`’s [specialized methods](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/AnimatingViews/AnimatingViews.html)

> `border-radius` CSS property for &lt;input&gt; <—> `UITextField`’s [`borderStyle`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextField_Class/#//apple_ref/occ/instp/UITextField/borderStyle) set to `UITextBorderStyleRoundedRect`

> `border-radius` CSS property in general <—> [`layer.cornerRadius`](https://developer.apple.com/library/mac/documentation/GraphicsImaging/Reference/CALayer_class/index.html#//apple_ref/occ/instp/CALayer/cornerRadius)


That’s all I found useful and worth mentioning here, but of course the full list of styling-related features of UIKit, and especially when going down to CALayers is vast, likewise the full list of features in CSS, so I don’t think it’s even feasible to cover everything here.


Next time I’ll conclude the series with [the post about events handling](/ios-layouts-for-web-developers-5-events-handling/).
