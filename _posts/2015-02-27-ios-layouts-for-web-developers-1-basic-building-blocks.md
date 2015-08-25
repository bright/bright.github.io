---
layout: post
excerpt: "In the first part of iOS layouts for web developers series I'll have a look at the basic building blocks of the view layer in iOS and compare it to what HTML offers. First, we need to shift our mindset a bit and accept the fact we need to give up some control over our views to the iOS."
title: "iOS layouts for web developers #1 - basic building blocks"
modified: 2015-02-27
tags: [iOS]
comments: true
author: adam
---

In the first part of [**iOS layouts for web developers** series](/ios-layouts-for-web-developers/) I'll have a look at the basic building blocks of the view layer in iOS and compare it to what HTML offers. First, we need to shift our mindset a bit and accept the fact we need to give up some control (pun intended) over our views to the iOS.

Elements vs. controls
----

In HTML, everything we can see in the browser is built upon the low-level HTML elements (tags) organized within a hierarchy. The browser draws the elements visual representations according to the associated CSS stylesheet definitions, intrinsic size of the content within a control and the document flow.

In iOS for composing views we're mostly using the [UIKit framework](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIKit_Framework/). What we see on the screen also comprises a hierarchy - its elements are [`UIView`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/) instances or instances of its subclasses. View is a concept on the higher level of abstraction than single HTML element. `UIView` is the generic building block, it may be thought of as `<div>` equivalent - by default it serves as just a container to hold another elements, but it’s visual aspects can be freely customized.

> `<div>` <—> `UIView`

UIKit offers a lot of out-of-box functionality available as controls. All specialized controls are implemented as `UIView` subclasses. In most cases, controls can’t be treated as an equivalent of HTML elements. Controls conveys both visual and functional aspects - it encapsulates it together in a way more similar to Web Components or widgets than a single HTML tag. The [catalog of ready-to-use built-in controls](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/) resembles [jQuery UI](http://jqueryui.com/) library - there are popups, buttons, sliders, activity indicators etc. Also, the environment specifics of iOS controls make it less concerned about semantics, as we have much less interoperability or compatibility constraints than in the open and unbounded web development. iOS control’s concern is about how it looks and behaves like, not what role it fulfils and what meaning it conveys to the structure of the screen.

> Web Component, jQuery UI widget <—> UIKit control (`UIView` subclass)

Because of the different level the view elements in iOS are defined compared to HTML, it’s not easy to create a translation table indicating what is the equivalent iOS control for the particular HTML element or set of elements. But let me try for the few basic ones.

First of all, it is worth noting that `UIView`, as the basic building block for iOS views, is powerful and configurable enough to achieve a lot itself. In HTML, there is a lot of block elements defined primarily for its semantical meaning - starting from document structure indicators like `<section>`, through `<address>` semantic blocks carrying some basic styling rules, up to `<hr>` to divide the content with the visual demarcator. In iOS, all these would be replaced with properly styled `UIView` (or its subclass [`UIControl`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIControl_Class/) that adds some basic control events handling, if this is necessary), stripped from its semantics. It feels a bit like using `<div>`s for everything, but aspects like accessibility are not within the primary focus here, it seems.

> most of block-level elements <—> just `UIView`/`UIControl`

Lists and tables
----

The first apparently obvious direct replacement for an HTML element could be [UITableView](http://www.idev101.com/code/User_Interface/UITableView/) for `<table>` structures. But it’s not that simple. `UITableView` should be more correctly called “`UIListView`” as in its basic form it is single column view - each row comprises a single cell. It’s main use case is to enable drill-down interface navigation style. It has nothing to do with the notion of tabular data, it is also widely and deliberately used as presentational element. Far from what we’re used to in HTML. Those aspects make `UITableView` something more equivalent to popular use case for `<nav>` or `<ul>` lists.

> `<nav>`, `<ul>` for navigational purposes <—> `UITableView` and its `UITableViewCell`s

Unfortunately, to emulate a real grid using `UITableView`, we’d need to nest custom views within a single [`UITableViewCell`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/) (that is, a view for a single row) - nothing available directly out-of-the-box. But `UITableView` also offers some aspects that are typical for tables and is known for us from what HTML offers - header rows, table headers & footers. Each of these table elements can be customized or even use an arbitrary custom view.

> `<th>` rows <—> section header in `UITableView`

> `<thead>`, `<tfoot>` <—> table header & table footer in `UITableView`

There is also an [`UICollectionView`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/) thing that is supposed to display a collection of items in more flexible manner - in the most cases it forms a grid of customizable [`UICollectionViewCell`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionViewCell_class/)s. It behaves like a set of elements with CSS float - it tries to put elements next to each other and wraps down when there’s no more space available. It’s still not real tabular data equivalent, but at least we’re not constrained to a single column layout.

> set of elements with `float` CSS property <—> `UICollectionView` and its `UICollectionViewCell`s

So what is the real equivalent of HTML tables? There’s nothing available out of the box, you need to resort to [specialized controls available](http://cocoapods.org/?q=grid). The separate broad topic here is how to present the tabular data effectively on the phone screen. I guess there are valid usability-related reasons to avoid tables at all.

> full-blown `<table>` <—> N/A

Forms and inputs
----

By default, the data entry and manipulation in HTML is handled within request-response cycles. One need to set up the values, hit “Save” and wait for the changes to be applied. On mobile platforms we’re used to another approach - things are saved immediately after the change happened, no “Save” button needed. That reduced the need for the equivalent of `<form>` demarcator element to exist. Form controls may be placed in any hierarchy and the application is responsible to handle the values changes on each control separately. Also, in iOS, there is no built-in fields grouping

> `<form>` <—> N/A

> `<input type=“hidden”>`, `<input type=“submit”>`, `<input type=“reset”>` <—> N/A

The [form controls set](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UIControl.html) from the visual point of view is more specialized in iOS than in pure HTML. There are distinct controls designed to input certain types of data, like [dates](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UIDatePicker.html) or [numerical values](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISlider.html). It is also covered by HTML5 specialized [input types](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input), but default UI is far simpler and not all cases are implemented even in the newest browsers.

Most textual input is based on [UITextField](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextField_Class/) that enables setting specific `keyboardType` to handle our expected content type best. For non-text inputs we have a set of standard widgets and our iOS users are already pretty used to its UX, so we should rather conform to that instead of precisely simulating something we know from web development.

> `<button>`, `<input type=“button”>`, `<a>` with `display: block` <—> `UIButton`

> `<input type=“checkbox”>` <—> `UISwitch`

> `<input type=“radio”>`, `<select>` <—> `UIPickerView`, `UIActionSheet`, `UISegmentedControl`

> `<input type=“text”>` <—> `UITextField`

> `<textarea>` <—> `UITextView`

> `<input type=“password”>` <—> `UITextField` with `secureTextEntry = YES`

> `<input type=“range”>` <—> `UISlider`, `UIStepper`

> `<input type=“email”>` <—> `UITextField` with `keyboardType = UIKeyboardTypeEmailAddress`

> `<input type=“url”>` <—> `UITextField` with `keyboardType = UIKeyboardTypeURL`

> `<input type=“tel”>` <—> `UITextField` with `keyboardType = UIKeyboardTypePhonePad`

> `<input type=“number”>` <—> `UITextField` with `keyboardType = UIKeyboardTypeDecimalPad`

> `<input type=“search”>` <—> `UITextField` with `keyboardType = UIKeyboardTypeWebSearch`

> `<input type=“date”>` <—> `UIDatePicker` with `datePickerMode = UIDatePickerModeDate`

> `<input type=“time”>` <—> `UIDatePicker` with `datePickerMode = UIDatePickerModeTime`

> `<input type=“datetime”>` <—> `UIDatePicker` with `datePickerMode = UIDatePickerModeDateTime`

One important type of HTML input we’ve left is `<input type=“file”>` that allows us to upload a file to the web server. There is no direct equivalent in iOS because of the specifics of mobile platforms. We should not expect our users to even be aware that they have some “files” on their phones. What we can expect instead, for example, is to choose a photo from the gallery or take a new one using the camera built-in into their device. iOS provides ready-to-use component for that - [`UIImagePickerController`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImagePickerController_Class/). If we need something less standard, we’d need to resort to 3rd-party controls.

> `<input type=“file”>` for images <—> `UIImagePickerController`

Images and drawings
----

To display an image in HTML, we use either `<img>` if the image is part of the content or CSS `background-image` if the image has purely presentational purpose. In iOS we do not have that semantic distinction, again, and although we can also [put images into backgrounds](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIColor_Class/index.html#//apple_ref/occ/clm/UIColor/colorWithPatternImage:), in most cases we just use [`UIImageView`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImageView_Class/) control. It gets [`UIImage`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImage_Class/) instance, in most cases initialized with the name of the resource we want to use. Out of the box, the image with correct resolution depending on the device screen dimensions is used, if available - something we’re still in early adoption phase in HTML with [`<picture>` element](http://html5hub.com/html5-picture-element/).

> `<img>`, `<picture>` <—> `UIImageView`

In case we need to draw something dynamically or just put some shape on the screen without resorting to images, in iOS we can always go one abstraction level deeper. Every `UIView` has its underlying [`CALayer`](http://www.raywenderlich.com/2502/calayers-tutorial-for-ios-introduction-to-calayers-tutorial) structure. All the views are built upon `CALayer`s. A layer represents the drawable rectangle on the screen that can be freely manipulated. It allows us to add shadows, borders, corner radius etc., so it might be seen as a replacement for some parts of CSS. There is also even lower level API available - [Core Graphics framework](http://www.raywenderlich.com/32283/core-graphics-tutorial-lines-rectangles-and-gradients). It corresponds more to what `<canvas>` offers, but is available for every control.

> CSS shadows, borders, corner radius etc. <—> `CALayer`

> `<canvas>` <—> Core Graphics functions

Inline elements equivalents
----

There is no view markup in iOS, so naturally there is no such thing as “free” textual content we know from HTML. All the text we want to display needs to be placed within some control, most likely within [`UILabel`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/). From the web developer standpoint, it behaves like a block element - it has its intrinsic size and needs to be positioned, so it’s more like `<p>` with `display: block`. It also enables us to manipulate a lot of formatting aspects of the text block, standing in place of some text-formatting aspects of CSS and inline text elements like `<em>`, `<strong>` etc.

> `<p>` with `display: block` <—> `UILabel`

Moreover, thanks to the [`NSAttributedString`](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSAttributedString_Class/) construct set via [`attributedText`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILabel_Class/#//apple_ref/occ/instp/UILabel/attributedText) property, it actually allows even parts of the text to look differently, making it equivalent to the whole soup of various HTML tags and content at once. But one have to admit, while [this is powerful](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/AttributedStrings/Articles/standardAttributes.html), the code [gets hairy pretty quickly](http://ios.eezytutorials.com/nsattributedstring-by-example.php), not to mention how [cumbersome](http://stackoverflow.com/questions/22854922/translate-attributed-string) is to localize it while operating on substring ranges directly. But there’s a nice workaround that will please us, web developers - a way to [create `NSAttributedString` directly from HTML](http://stackoverflow.com/questions/4217820/convert-html-to-nsattributedstring-in-ios). It has some drawbacks, but we may give it a try.

> `<em>`, `<i>`, `<strong>`, `<b>`, `<small>`, `<big>` <—> `NSAttributedString` with `NSFontAttributeName`

> `<mark>` <—> `NSAttributedString` with `NSBackgroundColorAttributeName`

> `<sub>`, `<sup>` <—> `NSAttributedString` with `NSSuperscriptAttributeName`

> `<ins>`, `<del>` <—> `NSAttributedString` with `NSUnderlineStyleAttributeName`

[Next time we’ll try to tackle controls positioning](/ios-layouts-for-web-developers-2-control-positioning/). Stay tuned!
