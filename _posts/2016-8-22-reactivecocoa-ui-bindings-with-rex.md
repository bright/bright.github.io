---
layout: post
title: ReactiveCocoa UI bindings with Rex
tags: iOS
comments: true
author: eliasz
---
Today, we will take a closer look at [Rex](https://github.com/RACCommunity/Rex) - ReactiveCocoa extensions. I find Rex pretty helpful when working with ReactiveCocoa, especially creating UI bindings.

If you are binding your view model with UI layer, Rex will let you do it much easier with it's extensions to `UIControls`. Here are some examples of `Rex` usage.

UIButton
---
{% highlight swift %}
let cocoaAction = CocoaAction(action) { _ in }

//without Rex
button.addTarget(cocoaAction, action: CocoaAction.selector, forControlEvents: .TouchUpInside)

//with Rex extensions
button.rex_pressed.value = cocoaAction
{% endhighlight %}

UITextField, UILabel, MutableProperty
---
{% highlight swift %}
var titleValue = MutableProperty<String?>(nil)

//without Rex
textField.rac_textSignal().subscribeNext {
  self.titleValue.value = $0 as? String
}

titleValue.producer.startWithNext {
  self.label.text = $0
  self.label.hidden = $0?.characters.count < 5
}

//with Rex
titleValue <~ textField.rex_text
titleLabel.rex_text <~ titleValue
titleLabel.rex_hidden <~ titleValue.producer.map({ $0?.characters.count < 5 })
{% endhighlight %}

You can clearly see that `Rex` makes our bindings much easier to read and understand. These are just examples, but you can find much more interesting properties like `rex_selectedSegmentIndex` for `UISegmentedControl` or `rex_on` for `UISwitch`. Moreover, `Rex` comes with some handy signal transformations, so go check it out!

*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
