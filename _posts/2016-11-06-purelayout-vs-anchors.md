---
layout: post
title: PureLayout vs NSLayoutAnchor - Great confrontation
tags: iOS
comments: true
author: eliasz
---

Last week I've made basic comparison between two libraries that will help you layout your interfaces - PureLayout and SnapKit. You can find this comparison [here](https://eliaszsawicki.com/purelayout-vs-snapkit/). Today I'd lake to take the same examples and see how they work with `NSLayoutAnchor`. `NSLayoutAnchor` is available to us since iOS 9 and provides us with a new way of creating your constraints. If you do not like creating `NSLayoutConstraints` using it's initializers or visual format, and do not want any external dependencies for your layout, then `NSLayoutAnchor` is for you!

Note before we start:

  - We need to set `translatesAutoresizingMaskIntoConstraints` to `false` for each view we want to add constraints to.
  - Each constraint that we create the "anchor" way has to be activated. That's why we use `.isActive = true`.


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

func anchorLayout() {
    box.translatesAutoresizingMaskIntoConstraints = false
    circle.translatesAutoresizingMaskIntoConstraints = false
    longer.translatesAutoresizingMaskIntoConstraints = false

    box.topAnchor.constraint(equalTo: view.topAnchor, constant: 50).isActive = true
    box.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 20).isActive = true
    box.widthAnchor.constraint(equalToConstant: 100).isActive = true
    box.heightAnchor.constraint(equalToConstant: 100).isActive = true

    circle.topAnchor.constraint(equalTo: box.topAnchor).isActive = true
    circle.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -20).isActive = true
    circle.widthAnchor.constraint(equalToConstant: 100).isActive = true
    circle.heightAnchor.constraint(equalToConstant: 100).isActive = true

    longer.topAnchor.constraint(equalTo: box.bottomAnchor, constant: 40).isActive = true
    longer.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 20).isActive = true
    longer.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -20).isActive = true
    longer.heightAnchor.constraint(equalToConstant: 40).isActive = true
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

func anchorLayout() {
    scrollView.translatesAutoresizingMaskIntoConstraints = false
    box.translatesAutoresizingMaskIntoConstraints = false
    circle.translatesAutoresizingMaskIntoConstraints = false
    longer.translatesAutoresizingMaskIntoConstraints = false

    scrollView.rightAnchor.constraint(equalTo: view.rightAnchor).isActive = true
    scrollView.topAnchor.constraint(equalTo: view.topAnchor).isActive = true
    scrollView.leftAnchor.constraint(equalTo: view.leftAnchor).isActive = true
    scrollView.bottomAnchor.constraint(equalTo: view.bottomAnchor).isActive = true
    scrollView.bottomAnchor.constraint(equalTo: longer.bottomAnchor, constant: 20).isActive = true

    box.topAnchor.constraint(equalTo: scrollView.topAnchor, constant: 50).isActive = true
    box.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 20).isActive = true
    box.widthAnchor.constraint(equalToConstant: 100).isActive = true
    box.heightAnchor.constraint(equalToConstant: 100).isActive = true

    circle.topAnchor.constraint(equalTo: box.topAnchor).isActive = true
    circle.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -20).isActive = true
    circle.widthAnchor.constraint(equalToConstant: 100).isActive = true
    circle.heightAnchor.constraint(equalToConstant: 100).isActive = true

    longer.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 20).isActive = true
    longer.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -20).isActive = true
    longer.topAnchor.constraint(equalTo: box.bottomAnchor, constant: 300).isActive = true
    longer.heightAnchor.constraint(equalToConstant: 200).isActive = true
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

func anchorLayout() {
    scrollView.translatesAutoresizingMaskIntoConstraints = false
    box.translatesAutoresizingMaskIntoConstraints = false
    circle.translatesAutoresizingMaskIntoConstraints = false
    longer.translatesAutoresizingMaskIntoConstraints = false

    scrollView.rightAnchor.constraint(equalTo: view.rightAnchor).isActive = true
    scrollView.topAnchor.constraint(equalTo: view.topAnchor).isActive = true
    scrollView.leftAnchor.constraint(equalTo: view.leftAnchor).isActive = true
    scrollView.bottomAnchor.constraint(equalTo: view.bottomAnchor).isActive = true
    scrollView.bottomAnchor.constraint(equalTo: longer.bottomAnchor, constant: 20).isActive = true

    box.topAnchor.constraint(equalTo: scrollView.topAnchor, constant: 150).isActive = true
    box.centerXAnchor.constraint(equalTo: scrollView.centerXAnchor).isActive = true
    box.widthAnchor.constraint(equalToConstant: 100).isActive = true
    box.heightAnchor.constraint(equalToConstant: 100).isActive = true

    circle.bottomAnchor.constraint(lessThanOrEqualTo: view.bottomAnchor, constant: -20).isActive = true
    circle.bottomAnchor.constraint(lessThanOrEqualTo: longer.topAnchor, constant: -20).isActive = true
    circle.centerXAnchor.constraint(equalTo: scrollView.centerXAnchor).isActive = true
    circle.widthAnchor.constraint(equalToConstant: 100).isActive = true
    circle.heightAnchor.constraint(equalToConstant: 100).isActive = true

    longer.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 20).isActive = true
    longer.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -20).isActive = true
    longer.topAnchor.constraint(equalTo: box.bottomAnchor, constant: 660).isActive = true
    longer.heightAnchor.constraint(equalToConstant: 200).isActive = true
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

var buttonLeftConstraint: NSLayoutConstraint?

func anchorLayout() {
    animateButton.translatesAutoresizingMaskIntoConstraints = false
    buttonLeftConstraint = animateButton.leftAnchor.constraint(lessThanOrEqualTo: view.leftAnchor, constant: 20)
    buttonLeftConstraint?.isActive = true
    animateButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50).isActive = true
    animateButton.widthAnchor.constraint(equalToConstant: 100).isActive = true
    animateButton.heightAnchor.constraint(equalToConstant: 100).isActive = true
}

func animate() {
    UIView.animate(withDuration: 1) {
        let random: CGFloat = CGFloat(arc4random_uniform(200))
        self.buttonLeftConstraint?.constant = random
        self.view.layoutIfNeeded()
    }
}

{% endhighlight %}

Conclusion
---

I'm really glad that we have `NSLayoutAnchor`. If you want to create layout in code and avoid any external layout libraries to do this, then `NSLayoutAnchor` is a valid choice! It won't be as neat and comfy as using `PureLayout` or `SnapKit`, but for me it's much much better than visual format or basic `NSLayoutConstraint` initializer.

What do you think of NSLayoutAnchor? How do you layout your views? Share your thoughts in comments!

*This article is cross-posted with my [my personal blog](https://eliaszsawicki.com/).*
