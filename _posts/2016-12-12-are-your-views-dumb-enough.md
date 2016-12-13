---
layout: post
title: Are your views dumb enough? — A way to run your tests without simulator
tags: iOS
comments: true
author: eliasz
---

![Header](/images/are-views-dumb-enough/turbo.jpg){: .center-image}

Hi! As you can see, the title of this post consists of two parts. “Are your views dumb enough” refers to managing code between your classes in project, which is really interesting topic, but there is also a second part — “A way to run your tests without simulator”. Managing your code is pretty straight forward topic and you probably know what to expect from this part, but how do I want to run my tests without simulator? Isn’t a simulator something we really need to test an application? Turns out it is not!

Are your views dumb enough?
---
This is a simple model that will store information about a person.
<script src="https://gist.github.com/Eluss/9d6ae51c95789be142e1136cb07a176f.js"></script>


Below you can see a view (I will omit applying constraints here) that we will use in order to display information about our Person. What we have to take care of is:

1. Set proper text for our labels
2. Perform work in workButtonTapped which is invoked after tap event

<script src="https://gist.github.com/Eluss/f72bd4830a6f0a818281c468f666ee94.js"></script>

Ok! Pretty much done! We can easily add this view to our app and play with it. That was not so hard!

Problems begin…
---
But wait! Suddenly something bad happened! Someone decided, that we would should reuse the layout we prepared, fill it with data from the same model, but displayed just a bit different. Well… It’s pretty hard to do it here, as we already decided how to present our title and what is the format for a birthday date. The logic behind our button is also here…

![Knew too much](/images/are-views-dumb-enough/knew-too-much.jpg)

The problem here is that our view has too much knowledge about what is happening! How can we change it? Let’s take a look at MVVM pattern that will allow us to move this knowledge to another place.
[You can find more about architecture patterns here](https://medium.com/ios-os-x-development/ios-architecture-patterns-ecba4c38de52#.fbormp201)

<script src="https://gist.github.com/Eluss/0446c0be1eb12a0fc972a15eaebd31be.js"></script>

Ok, much better now. We now have protocol that defines abilities of our PersonViewModel. Suddenly, our view became pretty dumb. It should only know how to layout things, but it should not have knowledge about a logic behind the content that view is populated with . Every time it wants any job to be performed, it has to ask object hidden behind PersonViewModel protocol. Let’s take a look at someone who implements this protocol.

<script src="https://gist.github.com/Eluss/fc0212d0861c469b9a7e38046d10f633.js"></script>

Ok, we now see two different implementations of PersonViewModel protocol. Now we can reuse the same view with different logic behind it.

![Separate](/images/are-views-dumb-enough/yoda-separate.jpeg)

Making your views dumb is a great way to achieve cleaner code that is much easier to test. Now we can easily test our ViewModels to see if they work as intended. This is all possible thanks to separation of our business logic from layout!

A way to run your tests faster!
---
You probably noticed that every time you run your test target for your iOS application, a simulator is launched as well. You get used to that after some time and that’s ok. It just takes longer. And we have to run simulator anyway… Or do we?
Why is simulator launched every time we run our tests?
Well… we need it for a UIKit that is a dependency in our app. UIKit is an iOS specific library.
But we do not have UIKit in our logic code any more, do we?
Does that mean that we should be able to test our application logic without the need of launching a simulator?

The answer is YES!
---

![Separate](/images/are-views-dumb-enough/running-tests.jpg)

Recently I started thinking about separating a big project intro many smaller frameworks. If my logic is not tightly coupled, then this should not be a big problem, and if you have this kind of separation then there is a high chance that your code is written well. What if my little frameworks do not have dependency on UIKit? I can easily reuse them on different platforms like iOS, MacOS etc. This is also an approach that you can use to test these frameworks. 
If framework can be used on MacOS, then you are also able to test them there! That means you do not need simulator anymore!
How do we prepare framework that will target iOS and MacOS? Below you’ll find a post about how to create cross-platform frameworks.
[Here is a link to “Cross-Platform Frameworks in Xcode” by Anthony Colangelo](https://acolangelo.com/blog/cross-platform-frameworks-in-xcode)

After creating a framework with your logic, here is what is left to do:

![Separate](/images/are-views-dumb-enough/test-target.png)

1. Create a target for OSX Unit Testing Bundle
2. As target to be tested select framework that has MacOS as destination

Done!
---
By having a separate framework for a business logic of our application we gained:
Ability to test our logic without need to launch simulator
Reusable code between different platforms
Cleaner code which is easier to understand in parts

Conclusion
---

I believe that this way of structuring your code can benefit you a lot and will result in much cleaner code and tests that will not require that much time to run. The approach to running tests without simulator is a kind of “proof of concept” for me that I wanted to share with you. I’m willing to try it out more on the battlefield and see how it holds. 

How do you solve these issues? Do you have experience in running test this way? I’d be glad if you share it in comments section below ;)

*This article is cross-posted with my [my personal blog](https://eliaszsawicki.com/).*
