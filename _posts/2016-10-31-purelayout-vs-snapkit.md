---
layout: post
title: PureLayout vs SnapKit - Great confrontation
tags: iOS
comments: true
author: eliasz
---

At first, let me clear something out. I'm heavy PureLayout user. I've been creating my UIs in code for some time now and it's not looking like I'm going back to Interface Builder any time soon. I'm not saying IB is bad, but it's just not the way that I do things. I started working with PureLayout back in Objective-C days and I kept on using it in Swift as well. However, recently I've been interested in a framework called SnapKit, that offers a nice "swifty" way of building views in your application. This post is my way of getting into SnapKit. Below, you can find some UI examples, that I've written using both PureLayout and SnapKit.

Simple positioning
---

![Simple positioning](/images/simple-positioning.png)

{% highlight swift%}

func pureLayout() {
    box.autoPinEdge(toSuperviewEdge: .top, withInset: 50)
    box.autoPinEdge(toSuperviewEdge: .left, withInset: 20)
    box.autoSetDimensions(to: CGSize(width: 100, height: 100))

    circle.autoPinEdge(.top, to: .top, of: box)
    circle.autoPinEdge(toSuperviewEdge: .right, withInset: 20)
    circle.autoSetDimensions(to: CGSize(width: 100, height: 100))

    longer.autoPinEdge(toSuperviewEdge: .left, withInset: 20)
    longer.autoPinEdge(toSuperviewEdge: .right, withInset: 20)
    longer.autoPinEdge(.top, to: .bottom, of: box, withOffset: 40)
    longer.autoSetDimension(.height, toSize: 40)
}

func snapKit() {
    box.snp.makeConstraints { (make) in
        make.top.equalTo(self.view).inset(50)
        make.left.equalTo(self.view).inset(20)
        make.size.equalTo(CGSize(width: 100, height: 100))
    }

    circle.snp.makeConstraints { (make) in
        make.top.equalTo(self.box)
        make.right.equalTo(self.view).inset(20)
        make.size.equalTo(CGSize(width: 100, height: 100))
    }

    longer.snp.makeConstraints { (make) in
        make.left.right.equalTo(self.view).inset(20)
        make.top.equalTo(self.box.snp.bottom).offset(40)
        make.height.equalTo(40)
    }
}

{% endhighlight %}

Inside UIScrollView
---

![ScrollView positioning](/images/Scroll.gif)

{% highlight swift %}

func pureLayout() {
    scrollView.autoPinEdgesToSuperviewEdges(with: .zero)
    scrollView.autoPinEdge(.bottom, to: .bottom, of: longer, withOffset: 20)

    box.autoPinEdge(toSuperviewEdge: .top, withInset: 50)
    box.autoPinEdge(.left, to: .left, of: self.view, withOffset: 20)
    box.autoSetDimensions(to: CGSize(width: 100, height: 100))

    circle.autoPinEdge(.top, to: .top, of: box)
    circle.autoPinEdge(.right, to: .right, of: self.view, withOffset: -20)
    circle.autoSetDimensions(to: CGSize(width: 100, height: 100))

    longer.autoPinEdge(.left, to: .left, of: self.view, withOffset: 20)
    longer.autoPinEdge(.right, to: .right, of: self.view, withOffset: -20)
    longer.autoPinEdge(.top, to: .bottom, of: box, withOffset: 300)
    longer.autoSetDimension(.height, toSize: 200)
}

func snapKit() {
    scrollView.snp.makeConstraints { (make) in
        make.edges.equalTo(self.view)
        make.bottom.equalTo(longer).offset(20)
    }

    box.snp.makeConstraints { (make) in
        make.top.equalTo(self.scrollView).inset(50)
        make.left.equalTo(self.view).inset(20)
        make.size.equalTo(CGSize(width: 100, height: 100))
    }

    circle.snp.makeConstraints { (make) in
        make.top.equalTo(self.box)
        make.right.equalTo(self.view).inset(20)
        make.size.equalTo(CGSize(width: 100, height: 100))
    }

    longer.snp.makeConstraints { (make) in
        make.left.right.equalTo(self.view).inset(20)
        make.top.equalTo(self.box.snp.bottom).offset(300)
        make.height.equalTo(200)
    }
}


{% endhighlight %}

UIScrollView with a surprise
---

![ScrollView positioning](/images/ScrollViewSurprise.gif)

{% highlight swift %}

func pureLayout() {
    scrollView.autoPinEdgesToSuperviewEdges(with: .zero)
    scrollView.autoPinEdge(.bottom, to: .bottom, of: longer, withOffset: 20)

    box.autoPinEdge(toSuperviewEdge: .top, withInset: 150)
    box.autoAlignAxis(toSuperviewAxis: .vertical)
    box.autoSetDimensions(to: CGSize(width: 100, height: 100))

    circle.autoPinEdge(.bottom, to: .bottom, of: self.view, withOffset: -20, relation: .lessThanOrEqual)
    circle.autoPinEdge(.bottom, to: .top, of: longer, withOffset: -20, relation: .lessThanOrEqual)
    circle.autoAlignAxis(toSuperviewAxis: .vertical)
    circle.autoSetDimensions(to: CGSize(width: 100, height: 100))

    longer.autoPinEdge(.left, to: .left, of: self.view, withOffset: 20)
    longer.autoPinEdge(.right, to: .right, of: self.view, withOffset: -20)
    longer.autoPinEdge(.top, to: .bottom, of: box, withOffset: 660)
    longer.autoSetDimension(.height, toSize: 200)
}

func snapKit() {
    scrollView.snp.makeConstraints { (make) in
        make.edges.equalTo(self.view)
        make.bottom.equalTo(longer).offset(20)
    }

    box.snp.makeConstraints { (make) in
        make.top.equalTo(self.scrollView).offset(150)
        make.centerX.equalTo(self.view)
        make.size.equalTo(CGSize(width: 100, height: 100))
    }

    circle.snp.makeConstraints { (make) in
        make.bottom.lessThanOrEqualTo(self.view).offset(-20)
        make.bottom.lessThanOrEqualTo(self.longer.snp.top).offset(-20)
        make.centerX.equalTo(self.view)
        make.size.equalTo(CGSize(width: 100, height: 100))
    }

    longer.snp.makeConstraints { (make) in
        make.left.right.equalTo(self.view).inset(20)
        make.top.equalTo(self.box.snp.bottom).offset(660)
        make.height.equalTo(200)
    }
}

{% endhighlight %}

Updating constraint's constant
---

![ScrollView positioning](/images/move.gif)

{% highlight swift%}

var buttonLeftConstraint: NSLayoutConstraint?

func pureLayout() {
    buttonLeftConstraint = animateButton.autoPinEdge(toSuperviewEdge: .left, withInset: 20)
    animateButton.autoPinEdge(toSuperviewEdge: .top, withInset: 50)
    animateButton.autoSetDimensions(to: CGSize(width: 100, height: 100))
}

func animate() {
    UIView.animate(withDuration: 1) {
        let random: Double = Double(arc4random_uniform(200))
        self.buttonLeftConstraint?.constant = random
        self.view.layoutIfNeeded()
    }
}

///

var buttonLeftConstraint: Constraint?

func snapKit() {
    animateButton.snp.makeConstraints { (make) in
        buttonLeftConstraint = make.left.equalTo(self.view).offset(20).constraint
        make.top.equalTo(self.view).inset(50)
        make.size.equalTo(CGSize(width: 100, height: 100))
    }
}

func animate() {
    UIView.animate(withDuration: 1) {
        let random: Double = Double(arc4random_uniform(200))
        self.buttonLeftConstraint?.update(offset: random)
        self.view.layoutIfNeeded()
    }
}

{% endhighlight %}

Conclusion
---

Looking at those examples, you can decide which style of UI you prefer. Personally, I must admit that I really like the way that SnapKit works. I won't be rewriting my existing PureLayout to SnapKit codebase, but I'll surely take SnapKit into consideration, when starting a new project.

*This article is cross-posted with my [my personal blog](https://eliaszsawicki.com/).*
