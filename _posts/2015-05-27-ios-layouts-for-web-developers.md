---
layout: post
excerpt: "Recently I had an opportunity to dive into an iOS development and while I enjoy it, I miss a lot of things from the web development world. I was looking for an iOS begginer guide targeted specifically to the web developers like me, but I haven't found any. This is how the idea for this series of blog posts was born."
title: "iOS layouts for web developers"
date: 2015-05-27
tags: [iOS]
comments: true
author: adam
---

Almost all of my past development experience is centered around the web. Just recently I had an opportunity to dive into an iOS development and while I enjoy it, I miss a lot of things from the web development world. I've quickly discovered that applying the techniques and approaches directly from the web is often not possible. Sometimes I had to switch to the different mindset than the one I'm used to. To make things easier, I was looking for an iOS begginer guide targeted specifically to the web developers like me, but I haven't found any. This is how the idea for this series of blog posts was born.

I’ve tried to go through multiple layout-specific aspects of iOS programming and compare it to the concepts I know from the web. My intention never was to replace Apple's documentation nor write an extensive guide to iOS - there are better resources for that. I also expected that the reader is already able to use Objective-C or Swift at the syntax level and has the basic knowledge of how iOS generally works. What I hoped to create is more a translation table between the similar concepts on both platforms. As such, and also due my limited iOS experience, it for sure has some deficiencies and simplifications - I've treated this write-up as a learning opportunity, too.

1. **[Basic building blocks](/ios-layouts-for-web-developers-1-basic-building-blocks/)**

    The first part looks at the basic building blocks of the view layer in iOS and compares it to what HTML offers. In HTML, everything we can see in the browser is built upon the low-level HTML elements. In iOS for composing views we're mostly using the UIKit framework and its views and controls. View is a concept on the higher level of abstraction than single HTML element.

    UIKit offers a lot of out-of-box functionality available as controls. All specialized controls are implemented as UIView subclasses. In most cases, controls can’t be treated as an equivalent of HTML elements. Controls conveys both visual and functional aspects - it encapsulates it together in a way more similar to Web Components or widgets than a single HTML tag. However, some HTML elements can be compared quite directly and the post tries to find these analogies.

2. **[Control positioning](/ios-layouts-for-web-developers-2-control-positioning/)**

    The matter of how and where the controls are drawn on the screen in iOS is complex enough so that it got the separate post only about it, especially that it completely differs from what I knew from the web. The thing that struck me most when I was starting with iOS is that there is no such concept like web's document flow, giving the controls some default sizes and positions. Controls just don’t show up by default, unless we tell them explicitly where should they go and what size they should take.

    But that kind of positioning is quite directly equivalent to the absolute positioning in the web, with all its deficiencies. We lack flexibility, we have no way to adjust to different screen sizes and we always need to operate in fixed-sized coordinate plane. In the web, we strive not to use absolute positioning and we should try to avoid it in iOS, too. The post shows a few techniques to do so.

3. **[Managing the appearance](/ios-layouts-for-web-developers-3-managing-appearance/)**

    It was around early 2000s when CSS became ubiquitous in the web world. CSS is now an inseparable part of the web development, there’s virtually no way to do anything without it. Now, it’s 2015 and we’re looking at the iOS layouts. How do we maintain the content vs. presentation separation in iOS? The short answer is - we don’t. There is no direct replacement for CSS concept in iOS.

    I was seriously shocked when I first learned that fact. But the fact is, a lot of iOS community doesn’t care. Most iOS developers go “the Apple way” here and use graphical Interface Builder to design the layout with drag&drop. There are multitude of alternatives - some of them gives us a lot of power, but with the great power comes the great responsibility. Or, sometimes just the great clutter.

4. **[CSS properties replacements](/ios-layouts-for-web-developers-4-css-properties-replacements/)**

     Next in the series, I’m going through various visual aspects of the iOS controls and seeing how we can set it up, compared to CSS capabilities. The differences are quite significant, given the various positioning models and different approach to textual content.

5. **[Events handling](/ios-layouts-for-web-developers-5-events-handling/)**

    Finally, I’m looking at the hard topic of events handling. This time I have to admit it is harder when looking from the web perspective. The mobile web promise can’t be fulfilled until the state of the events handling catches up with what iOS offers. Contrary to low-level handling available for the web, events in iOS are approached at multiple levels of abstraction, giving the developer the way to either easily use the system default gestures or alternatively dive down to more complex but more powerful API.

    Both in the web and in iOS we employ event-based models to define and control the interactions between our application and the external world, especially the user. The general idea of listening and reacting to particular events on specific parts of the UI or the whole application is the same on both environments. But the set of events is distinct and the main difference we need to always be aware is there are different ways to interact with the classical web than with the mobile device.


That’s all I have now. As I wrote the series, I found the topic incredibly broad and I don’t even try to address and describe all the possible analogies between both platforms. Also, be aware that these posts are full of possibly broken simplifications and shortcuts - but I think to avoid that I’d need to write something 5 times longer and probably far less readable. Anyway, if you find this material useful and you see the field for improvement, please leave a comment or maybe even [a pull request](https://github.com/bright/bright.github.io/tree/master/_posts). Enjoy!
