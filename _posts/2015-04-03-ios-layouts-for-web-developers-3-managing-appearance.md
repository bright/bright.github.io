---
layout: post
excerpt: "In the web, we've left behind the times when the view specific definitions like fonts or colors were scattered all over through the HTML structure. Now we’re looking at the iOS layouts. How do we maintain the content vs. presentation separation in iOS?"
title: "iOS layouts for web developers #3 - managing the appearance"
date: 2015-04-03
tags: [iOS]
comments: true
author: adam
---

This is the third part of the [**iOS layouts for web developers** series](/ios-layouts-for-web-developers/). The [first part was about the controls](/ios-layouts-for-web-developers-1-basic-building-blocks/), the [second about its positioning](/ios-layouts-for-web-developers-2-control-positioning/). Now I’m going to tackle how to approach managing the controls appearance - something that we have CSS for in the web.

CSS equivalent?
----

CSS was first standardised back in 1996, it was around early 2000s when it became ubiquitous. We've then left behind the times when the `<center>` tag was used to align content and view specific definitions like fonts or colors were scattered all over through the HTML structure. For almost two decades, we clearly feel it wasn’t the good way of doing things and we now strive to maintain the separation of structure vs. presentation. CSS is now an inseparable part of the web development, there’s virtually no way to do anything without it. It’s like a skin for the human body, we don’t rather meet anyone without one and even if we meet, we’d probably be frightened and disgusted by the raw and bloody flesh visible.

Now, it’s 2015 and we’re looking at the iOS layouts. How do we maintain the content vs. presentation separation in iOS? The short answer is - we don’t. There is no direct replacement for CSS concept in iOS. I was seriously shocked when I first learned that fact. But the fact is, a lot of iOS community [doesn’t care](http://stackoverflow.com/questions/2767512/good-reasons-why-to-not-use-xib-files?rq=1). Most iOS developers go [“the Apple way”](http://www.techrepublic.com/blog/software-engineer/getting-started-with-ios-views-and-view-controllers-part-1/) here and use [Interface Builder](https://developer.apple.com/xcode/interface-builder/) to design the layout. They define the whole presentation layer by clicking and dragging, not even feeling the need of any code for that purpose.

> content vs. presentation separation and styles reuse using CSS <—> N/A

One might say that less code means less bugs, but of course, Interface Builder does nothing different than generating the code underneath. It’s in [XIB format](http://www.monobjc.net/xib-file-format.html), nothing easily “consumable” by anything other than its native generator. Trying to touch it from the outside [will probably hurt](http://stackoverflow.com/questions/1660606/is-it-possible-to-directly-edit-the-xml-of-xib-files). Working with the code generator like Interface Builder [can often encourage bad practices](http://blog.teamtreehouse.com/why-i-dont-use-interface-builder) - it creates tight coupling between view and it’s logic behind, it creates merge hell when touched by more than one person, it destroys the ability to reuse style definitions and the relation between what we did in the IDE and what we got in the final app is too implicit.

Also, maybe it’s only my personal delusion, but I think we in the web world feel a lot of disgust for WYSIWYGs in general and often even for using the mouse at all. CSS is not an imperative programming language (fortunately), but it’s still the code. We treat it as such, we edit it as a raw text files, we understand it and we feel comfortable with that fact. Whenever something automated messes with my CSS or HTML, I’m losing the confidence in what’s going on and how it works.

How to handle style definitions then?
----

When we decide not to go “the Apple way” and abandon Interface Builder striving for something that allows style definitions reuse and separation, our simplest alternative is to [create everything directly in the code](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ViewLoadingandUnloading/ViewLoadingandUnloading.html#//apple_ref/doc/uid/TP40007457-CH10-SW36), instantiating the controls “manually", setting its properties like colors, setting its frame or Auto Layout constraints and finally adding it to the superview, like this:

{% highlight Objective-C %}
- (void)loadView {
    [super loadView];

    UILabel *testView = [UILabel new];
    testView.backgroundColor = [UIColor redColor];
    testView.font = [UIFont boldSystemFontOfSize:24];
    testView.text = @"test";
    testView.translatesAutoresizingMaskIntoConstraints = NO;

    [self.view addSubview:testView];
    [testView sizeToFit];

    [self.view addConstraint:[NSLayoutConstraint constraintWithItem:testView
                                        attribute:NSLayoutAttributeTopMargin
                                        relatedBy:NSLayoutRelationEqual
                                           toItem:self.view
                                        attribute:NSLayoutAttributeTopMargin
                                       multiplier:1
                                         constant:20]];
    [self.view addConstraint:[NSLayoutConstraint constraintWithItem:testView
                                        attribute:NSLayoutAttributeLeading
                                        relatedBy:NSLayoutRelationEqual
                                           toItem:self.view
                                        attribute:NSLayoutAttributeLeading
                                       multiplier:1
                                         constant:20]];
}
{% endhighlight %}

For complex screens, it quickly grows to hundreds of lines of imperative and repetitive code that is inherently very declarative in its real nature. It hurts me every time I assign properties like `font` and `backgroundColor` etc. It feels just like I’d be putting `<font face=“Arial” color=“red”>` in my HTML markup and it’s already more than 10 years ago when I did so the last time. It’s like that human flesh visible outside.

One simple strategy we can employ is to enclose our repeatable patterns of style and layout definitions, very remotely equivalent to our CSS classes, as a static method somewhere or as [a category on `UIView`](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html):

{% highlight Objective-C %}
@implementation UILabel (Layout)

+ (UILabel *)bigRedLabel {
    UILabel *label = [UILabel new];
    label.backgroundColor = [UIColor redColor];
    label.font = [UIFont boldSystemFontOfSize:24];
    return label;
}

- (void)placeInTopLeftCornerOf:(UIView *)parentView {
    self.translatesAutoresizingMaskIntoConstraints = NO;
    [self sizeToFit];

    [parentView addConstraint:[NSLayoutConstraint constraintWithItem:self
                                        attribute:NSLayoutAttributeTopMargin
                                        relatedBy:NSLayoutRelationEqual
                                           toItem:parentView
                                        attribute:NSLayoutAttributeTopMargin
                                       multiplier:1
                                         constant:20]];
    [parentView addConstraint:[NSLayoutConstraint constraintWithItem:self
                                        attribute:NSLayoutAttributeLeading
                                        relatedBy:NSLayoutRelationEqual
                                           toItem:parentView
                                        attribute:NSLayoutAttributeLeading
                                       multiplier:1
                                         constant:20]];
}

@end
{% endhighlight %}

Now we can use it whenever we want our new control to look like the other:

{% highlight Objective-C %}
- (void)loadView {
    [super loadView];

    UILabel *testView = [UILabel bigRedLabel];
    testView.text = @"test";

    [self.view addSubview:testView];
    [testView placeInTopLeftCornerOf:self.view];
}
{% endhighlight %}

Apple proposal - appearance proxies
----

The issue of style definitions reuse was also approached by Apple within UIKit, albeit quite differently than in CSS. There’s a concept called [appearance](http://nshipster.com/uiappearance/). It allows us to define selected style properties of the controls once per the application, ensuring any customization we do is applied consistently across the app. We can define that all the buttons (`UIButton` instances) that we use in our app should have gray background:

{% highlight Objective-C %}
[UIButton appearance].backgroundColor = [UIColor grayColor];
{% endhighlight %}

We can restrict our customization scope to controls contained within another specific container control, for example to all `UIBarButtonItem`s within `UIToolbar`:

{% highlight Objective-C %}
[UIBarButtonItem appearanceWhenContainedIn:[UIToolbar class], nil]
    .tintColor = [UIColor blackColor];
{% endhighlight %}

Also, we can optionally make our appearance definitions dependent on the screen dimensions and orientations, using `UITraitCollection`s we’ve discussed [in the previous part of the series](/ios-layouts-for-web-developers-2-control-positioning/#the-real-responsiveness?):

{% highlight Objective-C %}
UITraitCollection *traitCollection = [UITraitCollection
    traitCollectionWithHorizontalSizeClass:UIUserInterfaceSizeClassCompact];
[UIImageView appearanceForTraitCollection:traitCollection]
    .tintColor = [UIColor redColor];
{% endhighlight %}

Appearance might be compared to CSS limited to elements only (no per-class or per-id definitions), with basic hierarchies and basic media queries support. However, the distance to the full-blown CSS equivalent is quite large. There is no way to apply appearance definitions to the selected subset of elements of a given type, like we do using CSS classes. There is no way to represent other types of relationships between controls than ancestor-descendant (`whenContainedIn`) - we need to forget about the selectors we know from CSS like siblings, direct children, n-th of type and so on. Moreover, when we have multiple ancestor-descendant relationships defined, [the first one encountered (most generic) is used](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/), while in CSS the most specific one is used, allowing us to specify our styles from the general definitions to the specialized ones going down the elements tree.

> CSS definitions on HTML elements <—> [UIAppearance](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/index.html#//apple_ref/doc/uid/TP40012857-UIView-SW16) definitions

> CSS definitions on HTML element hierarchies <—> UIAppearance definitions with `whenContainedIn` overloads; limited

> CSS definitions with screen size-related media queries <—> UIAppearance definitions with `forTraitCollection` overloads; limited

> CSS features other than above <—> N/A

Last but not least, customization via appearance is by default limited only to [several properties of the controls](https://gist.github.com/mattt/5135521) exposed by iOS with the `UI_APPEARANCE_SELECTOR` tag. For example, generic `UIView` only allows us to set `backgroundColor` through appearance and such a common control like `UILabel` doesn’t add anything more on its own. This means there is no out-of-the-box solution for setting text color, font properties, margins etc. for all the labels once across the application. That limitation can be overcome by defining an `UI_APPEARANCE_SELECTOR` [for custom properties](http://timross.info/blog/2013/03/26/more-fun-with-uiappearance/) and [for custom controls](http://petersteinberger.com/blog/2013/uiappearance-for-custom-views/), but that is far from the elegance of plain old declarative CSS.

Prospects for truly declarative solution
----

There are multiple projects out there in the open source space that aims to bring the real declarativeness, style reuse possibility and overall CSS feel to iOS views. Both [UISS](https://github.com/robertwijas/UISS) and [NUI](https://github.com/tombenner/nui) offer the syntax loosely inspired by CSS. The former is just a wrapper for UIAppearance with all its limitations. The latter goes much further, actually taking care of virtually all the styling exposed by UIKit controls. There are also [Pixate Freestyle](http://pixate.github.io/pixate-freestyle-ios/) and [nativeCSS](http://nativecss.com/) that offer direct usage of CSS within iOS apps. In Bright we’re also doing some in-house experiments in [Cero](https://github.com/bright/Cero), albeit it aims more to provide the declarative view hierarchy and the styling comes a bit as a side effect.

The most advanced solutions that features declarative and reusable styles are probably offered by the cross-platform frameworks that originate from the web. These frameworks promise the web developers a way to reuse some of our JavaScript, HTML and CSS skills directly on mobile platforms to produce native or semi-native experiences. Users of the platforms that employ HTML directly as a language to define apps views, like [PhoneGap/Cordova](http://coenraets.org/blog/phonegap-tutorial/), [Ionic](http://ionicframework.com/docs/components/) or [Framework7](http://www.idangero.us/framework7/) immediately benefit from having the real power of CSS available. Of course it comes with the penalty of being a thick and often limited or leaky abstraction.

On the other side there are frameworks that offer their own abstractions on views and styles. The one that [seems quite interesting](http://www.objc.io/issue-22/facebook.html) is [ComponentKit by Facebook](https://github.com/facebook/componentkit). It redefines the views in terms of components with declarative and composable API and [flexbox](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Flexible_boxes)-enabled styling. One more example can’t be missed here - there is a lot of buzz around recently released [React Native](http://jlongster.com/First-Impressions-using-React-Native) framework that offers its own approach of using JavaScript (and JSX known from [React.js](https://facebook.github.io/react/)) natively in iOS. With the power of JavaScript we’re familiar with, we can shape and reuse our presentation code in a more elegant way. React Native uses a [lightweight version of CSS](http://facebook.github.io/react-native/docs/style.html#content) that offers features like [composition](https://facebook.github.io/react-native/docs/style.html#content) and [flexbox](https://facebook.github.io/react-native/docs/flexbox.html).

I believe that all of these ideas and frameworks are shaping the future of iOS layouts and are worth keeping an eye on it with the hope that someday the native offering of iOS platform in terms of managing the appearance would not be so limited as today.

Next time - more on [how to achieve particular visual effects known from CSS within iOS views](/ios-layouts-for-web-developers-4-css-properties-replacements/).
