---
layout: post
excerpt: "Time to finish the iOS layouts for web developers series with the post about events. Both the web and iOS employ similar ideas, but the set of events is distinct and we need to be aware there are different ways to interact with the classical web than with the mobile device."
title: "iOS layouts for web developers #5 - events handling"
date: 2015-05-21
tags: [iOS]
comments: true
author: adam
---

Time to finish the [**iOS layouts for web developers** series](/ios-layouts-for-web-developers/) with the post about events. Earlier in the series you might read about [the controls](/ios-layouts-for-web-developers-1-basic-building-blocks/), [control positioning](/ios-layouts-for-web-developers-2-control-positioning/), [managing the appearance](/ios-layouts-for-web-developers-3-managing-appearance/) and [CSS properties replacements](/ios-layouts-for-web-developers-4-css-properties-replacements/).

Touchy state of the mobile touch events
----

Both in the web and in iOS we employ event-based models to define and control the interactions between our application and the external world, especially the user. The general idea of listening and reacting to particular events on specific parts of the UI or the whole application is the same on both environments. But the set of events is distinct and the main difference we need to always be aware is there are different ways to interact with the classical web than with the mobile device.

The web's basic [DOM events model](http://www.w3.org/TR/DOM-Level-2-Events/events.html#Events-eventgroupings) was designed when touch interfaces were still very far from mainstream devices. It assumes the application is controlled by the mouse and the keyboard, thus the events carry the information like where the mouse pointer is or which key was pressed. And now, when we use the web on the mobile device, we’re still constrained to what type of interaction the browser supports and in a lot of cases we still need to talk in terms of clicks and mouse moves instead of gestures.

The story about [proper touch events in the web](http://www.html5rocks.com/en/mobile/touch/) is [long and convoluted](http://www.w3.org/Mobile/mobile-web-app-state/#User_interactions). First, Safari introduced own [non-standard touch events support](https://developer.apple.com/library/safari/documentation/AppleApplications/Reference/SafariWebContent/HandlingEvents/HandlingEvents.html#//apple_ref/doc/uid/TP40006511-SW22) back in iOS 2.0. The de-facto standard was adopted by few other mobile browsers and later codified as the [W3C standard](http://www.w3.org/TR/touch-events/), but its adoption is [still quite poor](http://caniuse.com/#feat=touch). Meanwhile the new, more generic alternative concept of [Pointer Events](http://www.w3.org/TR/pointerevents/) was coined, specified and [introduced in Internet Explorer](http://caniuse.com/#feat=pointer). This led to the support fragmentation and uncertainty for developers what to rely on and what to expect in the future.

On the other hand we have native mobile platforms like iOS that know much better how the contemporary device is controlled. Instead of mouse and keyboard events, the mobile platform is concerned about [multitouch gestures like panning or pinching or accelerometer-based motion recognition](https://developer.apple.com/library/prerelease/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) and exposes it via the high-level gesture API instead of low-level information about each and every touch or finger movement.

Of course, on a conceptual level, there are some analogies and some of the most common native gestures can be translated quite directly into web world - the example is a scroll gesture, done by tapping the screen and dragging the app’s content - mobile browsers happen to emit `scroll` events then.

Simplified gestures handling
----

Events in iOS are approached at multiple levels of abstraction, giving the developer the way to either easily use the system default gestures or alternatively dive down to more complex but more powerful API.

The simpler layer available are [gesture recognizers](https://developer.apple.com/library/prerelease/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/GestureRecognizer_basics/GestureRecognizer_basics.html#//apple_ref/doc/uid/TP40009541-CH2-SW2). Views can have multiple gesture recognizers attached, each configured to detect a particular, possibly complex gesture like pinching ([`UIPinchGestureRecognizer`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPinchGestureRecognizer_Class/index.html#//apple_ref/occ/cl/UIPinchGestureRecognizer)) or finger rotation ([`UIRotationGestureRecognizer`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIRotateGestureRecognizer_Class/index.html#//apple_ref/occ/cl/UIRotationGestureRecognizer)). When the gesture is detected, the recognizer calls its target - the callback we defined when setting the recognizer up:

{% highlight Objective-C %}
- (void)viewDidLoad {
    [super viewDidLoad];

    UITapGestureRecognizer *doubleTapRecognizer = [[UITapGestureRecognizer alloc]
        initWithTarget:self action:@selector(respondToDoubleTapGesture:)];
    doubleTapRecognizer.numberOfTapsRequired = 2;
    [self.view addGestureRecognizer:doubleTapRecognizer];
}

- (void)respondToDoubleTapGesture:(id)sender {
    // react on double tap event here
}
{% endhighlight %}

There’s no direct analogy available for complex gestures in the web world. Mobile browser apps often have their own handling for such a gestures, not even passing the raw gesture to the web app currently opened. For example, when pinching the website on the mobile device, it is zoomed in or out [without the web app being notified](http://stackoverflow.com/questions/995914/catch-browsers-zoom-event-in-javascript) about that event directly. The solution to handle complex gestures in mobile web is to defer to 3rd-party libraries that employ the low-level handling mechanisms to emulate the gesture events. Examples are [Hammer.js](http://hammerjs.github.io/) or [Touchy](https://github.com/hotstudio/touchy).

> 3rd-party libraries to handle complex events <—> built-in gesture recognizers

Simple events simplified even more
----

For simpler events that do not require complex “recognizing” of movements pattern and consist just a single interaction, like tap, there is simplified mechanism available in iOS via `UIControl`'s [`addTarget:action:forControlEvents:`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIControl_Class/index.html#//apple_ref/occ/instm/UIControl/addTarget:action:forControlEvents:) method, where we specify callbacks for particular events directly in the control:

{% highlight Objective-C %}
- (void)viewDidLoad {
    [super viewDidLoad];

    UIButton *button = // create or find a button
    [button addTarget:self
               action:@selector(respondToTapGesture:)
     forControlEvents:UIControlEventTouchUpInside];
}

- (void)respondToTapGesture:(id)sender {
    // react on tap event here
}
{% endhighlight %}

The web analogy here is pretty clear - it’s like adding an event listener to the DOM element, either directly using [`element.addEventListener` API](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) or through convenient libraries like [jQuery and its `on` function](http://api.jquery.com/on/) etc.

> `addEventListener` and its wrappers <—> `addTarget:action:forControlEvents:`

What else we need to know here is the “translation table” from the DOM event to its [corresponding `UIControlEvent` type](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIControl_Class/index.html#//apple_ref/doc/constant_group/Control_Events). Of course the analogies are coarse-grained as always, but here we go:

> `mousedown` event <—> `UIControlEventTouchDown`

> `mouseup` event, conventionally also `click` event <—> `UIControlEventTouchUpInside`

> `change` event on form controls <—> `UIControlEventValueChanged`

> [drag events](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Drag_and_drop) <—> `UIControlEventTouchDragInside`, `UIControlEventTouchDragOutside`, `UIControlEventTouchDragEnter`, `UIControlEventTouchDragExit`

Low-level events handling
----

There is also lower level touch event handling available within [`UIResponder` base class](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIResponder_Class/#//apple_ref/doc/uid/TP40006783-CH4-SW13), which fortunately is a base class for `UIView` allowing these methods to be used everywhere. There are plenty of methods available to be overriden when our view implementation is interested in being notified about events like beginning of the touch ([`touchesBegan:withEvent:`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIResponder_Class/#//apple_ref/occ/instm/UIResponder/touchesBegan:withEvent:)), touch move ([`touchesMoved:withEvent:`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIResponder_Class/#//apple_ref/occ/instm/UIResponder/touchesMoved:withEvent:)) or touch end ([`touchesEnded:withEvent:`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIResponder_Class/#//apple_ref/occ/instm/UIResponder/touchesEnded:withEvent:)). These events consists of locations for all touch points, allowing multitouch support. But in order to detect any pattern, we need to analyse the data on our own. This resembles what we have [available in some web browsers](https://developer.mozilla.org/en-US/docs/Web/API/Touch_events).

> listening on `touchstart` event <—> overriding `touchesBegan:withEvent:` method

> listening on `touchmove` event <—> overriding `touchesMoved:withEvent:` method

> listening on `touchend` event <—> overriding `touchesEnded:withEvent:` method

> listening on `touchcancel` event <—> overriding `touchesCancelled:withEvent:` method

Raw [motion events](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIResponder_Class/#//apple_ref/doc/uid/TP40006783-CH4-SW19) handling is also available within `UIResponder` in the same fashion as touch events, using [`motionBegan:withEvent:`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIResponder_Class/#//apple_ref/occ/instm/UIResponder/motionBegan:withEvent:) method and its siblings. In the web we might use [device motion and orientation events](http://mobiforge.com/design-development/html5-mobile-web-device-orientation-events) instead.

> listening on `devicemotion` event <—> overriding `motionBegan:withEvent:` and `motionEnded:withEvent:` methods

Other events
----

For the sake of completeness, I’d mention few more event types that are somehow supported by both the web standard and iOS.

> listening on `window.onload`, jQuery `$(document).ready` or similar events <—> overriding [`UIViewController.viewDidLoad`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/#//apple_ref/occ/instm/UIViewController/viewDidLoad) method

> listening on `window.resize` event <—> overriding [`traitCollectionDidChange:`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITraitEnvironment_Ref/index.html#//apple_ref/occ/intfm/UITraitEnvironment/traitCollectionDidChange:) method on the view or view controller

> listening on [`deviceorientation`](https://developer.mozilla.org/en-US/docs/Web/API/Detecting_device_orientation) event <—> adding observer to `NSNotificationCenter` that observes for [`UIDeviceOrientationDidChangeNotification`](http://thinkdiff.net/iphones/orientation-detection-at-runtime-in-ipad-or-iphone/); alternatively overriding [`traitCollectionDidChange:`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITraitEnvironment_Ref/index.html#//apple_ref/occ/intfm/UITraitEnvironment/traitCollectionDidChange:) method on the view or view controller

> set of proposed solutions for [sensors and hardware integration](http://www.w3.org/Mobile/mobile-web-app-state/#Sensors_and_hardware_integration), not yet implemented <—> [remote control events on `UIResponder`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIResponder_Class/#//apple_ref/occ/instm/UIResponder/remoteControlReceivedWithEvent:)

Who handles the event?
----

Not only the types of events should be of our interest, but also which element/control is responsible for handling the event. We have some differences in that matter between the web and iOS.

In the web, all the interaction events are first dispatched directly to the element that user interacted with, for example the `mousedown` event is first delivered to the innermost DOM element user clicked with her mouse. By default, all the listeners registered for that element are fired and the event is passed up the DOM hierarchy (this is called event bubbling). Every listener has the opportunity to stop the bubbling (`stopPropagation`) and/or cancel other listeners, but by default events traverse up to the top of the view hierarchy (to the `document` element), regardless if the event was somehow handled or not.

In iOS, the way the event travels up the view hierarchy is similar - it is first delivered to the control down the tree and then passed up, but only until some control actually handles it - the traversal stops then.

> calling DOM event `stopPropagation` on event handling <—> on iOS, propagation is stopped automatically when the event is handled

Also, it is not fully accurate to say the events go up the view hierarchy. It actually go according to the [responder chain](https://developer.apple.com/library/prerelease/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/event_delivery_responder_chain/event_delivery_responder_chain.html#//apple_ref/doc/uid/TP40009541-CH4-SW2), which might or might not be equal to the view hierarchy order of controls. For touch events, unless we modify the [`nextResponder` property](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIResponder_Class/index.html#//apple_ref/occ/instm/UIResponder/nextResponder), it is the same. But we might want to manage which control is the next responder on our own, for example to implement nice [keyboard traversal through the text inputs](http://stackoverflow.com/questions/1347779/how-to-navigate-through-textfields-next-done-buttons).

> DOM events bubble up the DOM hierarchy <—> iOS events traverse according to the responder chain

But there are few more quirks. First one is the way the iOS determines which control was touched - it starts from the uppermost window view and [performs a hit-testing](https://developer.apple.com/library/prerelease/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/event_delivery_responder_chain/event_delivery_responder_chain.html#//apple_ref/doc/uid/TP40009541-CH4-SW4) on its subviews recursively. That takes into consideration only the views that are [actually visible within the view’s bounds](https://developer.apple.com/library/ios/qa/qa2013/qa1812.html) - so the views that are drawn outside its superview using `clipToBounds = NO` can’t handle the touch events properly. The workaround is to override [the hit-testing method](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/instm/UIView/hitTest:withEvent:), but this gets hairy pretty quickly.

> DOM events are delivered to the innermost element according to view hierarchy only <—> iOS touch events are delivered to the innermost control according both to view hierarchy and position within bounds

One more important trick that is often used to modify the default touch target control on iOS is to disable the [`userInteractionEnabled` flag](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/#//apple_ref/occ/instp/UIView/userInteractionEnabled) for the control to prevent it being considered for hit-testing. In that case, the control that gets the event might not actually be the innermost one, but the last of its ancestors that doesn’t have interaction disabled. To achieve something a bit similar in the web, we can set CSS `pointer-events: none` on the element we want to “disable", although this is rather rough analogy.

> `pointer-events: none` in CSS <—> `UIView`’s `userInteractionEnabled` flag


That’s all, folks. I hope anyone finds this series worth reading. I’d be grateful for any corrections, additions or just comments.
