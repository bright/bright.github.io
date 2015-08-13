---
layout: post
title: 'iOS: The simplest star rating control ever'
date: 2015-08-13
tags: [iOS]
comments: true
author: adam
excerpt: "Recently I needed to show the simple rating control in one of our iOS apps - the typical row of stars, few leftmost highlighted, the more highlighted, the better the rating is. My first thoughts were wandering around star images one after another, the hell with positioning with frames or multitude of Auto Layout constraints. Nah. I ended up with something much easier and elegant."
---

Recently I had a need to show the simple rating control in one of our iOS apps - the typical row of stars, few leftmost highlighted, the more highlighted, the better the rating is - something as obvious as this:

![star rating example](/images/star-rating-example.png)

My first thoughts were wandering around star images one after another, the hell with positioning with frames or multitude of [Auto Layout constraints](http://www.informit.com/articles/article.aspx?p=2041295). Nah. The second take led me to [`EDStarRating`](https://github.com/erndev/EDStarRating), a library that does this pretty well. But all I needed was only to show few stars in a row, all the events handling and customizations seemed a bit overkill for the simple task I was facing.

Then the aha! moment came. I can just play with [`NSAttributedString`](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSAttributedString_Class/), a fancy thing that adds a lot of formatting capabilities to ordinary string or even parts of it. And there is the awesome set of icons everyone's using anyway in [Font Awesome](http://fortawesome.github.io/Font-Awesome/icons/). So here are the steps I ended up doing:

## Add Font Awesome to your project

There are multiple ways how to do that. We decided to [add `FontAwesome.otf` definition file as a resource to our project](http://luciancancescu.blogspot.com/2015/03/how-to-integrate-fontawesome-in-your.html), because all I need is just to be able do the following:

{% highlight Objective-C %}
[UIFont fontWithName:@"FontAwesome" size:18.0f];
{% endhighlight %}

You might prefer to choose one of the [available CocoaPods](http://cocoapods.org/?q=font%20awesome) that esentially deal with the same thing, often with additional capabilities or sugar like enumeration for all the icons.

## Stargaze

The idea is to prepare the string that will contain the star icon multiple times, first few highlighted (filled), the rest greyed out or not filled. We need to find the appropriate Unicode numbers for our interesting icons, first - everything we need is [here](http://fortawesome.github.io/Font-Awesome/cheatsheet/):

![Font Awesome Unicode codes](/images/star-rating-fa.png)

I decided to use the basic `fa-star` icon (`\uf005`) and manipulate with the color, making the inactive gray. You might want to use `fa-star-o` instead (`\uf123`).

## Build the `NSAttributedString`

Now I need to create two formattings - first that represents the highlighted stars that will be black in my case and the second for the not highlighted ones - light gray, let's say. `NSAttributedString` expects me to provide the dictionaries with the appropriate options. Here they are:

{% highlight Objective-C %}
UIFont *fontAwesome = [UIFont fontWithName:@"FontAwesome" size:18.0f];
NSDictionary *activeStarFormatting = @{
    NSFontAttributeName : fontAwesome, 
    NSForegroundColorAttributeName : [UIColor blackColor]
};
NSDictionary *inactiveStarFormatting = @{
    NSFontAttributeName : fontAwesome, 
    NSForegroundColorAttributeName : [UIColor lightGrayColor]
};
{% endhighlight %}

Now, all I need is to build the actual string by iterating the appropriate number of times, first adding the highlighted stars, then filling with the inactive ones up to the expected number of stars.

{% highlight Objective-C %}
const NSInteger filledStars = 2;
const NSInteger maxStars = 3;

NSMutableAttributedString *attString = [NSMutableAttributedString new];
NSInteger i = 0;

for (; i < filledStars; ++i) {
    [attString appendAttributedString:[[NSAttributedString alloc] 
        initWithString:@" \uf005 " attributes:activeStarFormatting]];
}
for (; i < maxStars; ++i) {
    [attString appendAttributedString:[[NSAttributedString alloc] 
        initWithString:@" \uf005 " attributes:inactiveStarFormatting]];
}
{% endhighlight %}

## Enjoy!

Now it's just a matter of wrapping it in a nice function:

{% highlight Objective-C %}
+ (NSAttributedString *)starRatingWith:(NSInteger)filledStars
                            outOfTotal:(NSInteger)totalStars {
    UIFont *fontAwesome = [UIFont fontWithName:@"FontAwesome" size:18.0f];
    NSDictionary *activeStarFormatting = @{
            NSFontAttributeName : fontAwesome,
            NSForegroundColorAttributeName : [UIColor blackColor]
    };
    NSDictionary *inactiveStarFormatting = @{
            NSFontAttributeName : fontAwesome,
            NSForegroundColorAttributeName : [UIColor lightGrayColor]
    };

    NSMutableAttributedString *attString = [NSMutableAttributedString new];

    NSInteger i = 0;
    for (; i < filledStars; ++i) {
        [attString appendAttributedString:[[NSAttributedString alloc]
                initWithString:@" \uf005 " attributes:activeStarFormatting]];
    }
    for (; i < totalStars; ++i) {
        [attString appendAttributedString:[[NSAttributedString alloc]
                initWithString:@" \uf005 " attributes:inactiveStarFormatting]];
    }

    return attString;
}
{% endhighlight %}

To use it, we now just need an ordinary `UILabel`:

{% highlight Objective-C %}
UILabel *starRating = [UILabel new];
starRating.attributedText = [self starRatingWith:2 outOfTotal:3];
{% endhighlight %}

And here is the result:

![NSAttributedString-based star rating example](/images/star-rating-label.png)

Voilà!

