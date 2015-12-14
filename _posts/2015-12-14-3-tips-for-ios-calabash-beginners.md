---
layout: post
title: 3 tips for iOS Calabash beginners
tags: iOS
comments: true
author: eliasz
---

Have you ever felt that your app needs UI tests? Are you tired of checking behaviours of your application again and again? Consider using Calabash!

What is Calabash? It is an open source framework that is constantly supported by Xamarin. Calabash will let you run your UI tests written in Cucumber on iOS and Android. Moreover, it's really easy to read for people that are not familiar with programming.

If you started writing UI tests in Calabash and it isn't going as smooth as you expected, then check out this 3 tips, which helped me at the beginning.

####1. Use Calabash console!
	
Before you write any tests for your application, use Calabash console to play with your app a bit. You can perform many actions like swiping, entering text, scrolling the screen or touching the components before you write anything in your feature file. This will help you learn how to navigate through the app using Calabash and understand the query language much better before writing your own custom steps. In order to be able to perform all the gestures, you have to launch your console by executing `calabash-ios console` and after this, typing `start_test_server_in_background`, which will launch your app with touches enabled.

####2. Add accessibility identifiers to created UI objects!

There are many ways of refering to UI components while writing tests in Calabash, but we would like to use the one that we can rely on. You can easily refer to any object using text that is displayed in it (labels), but it is not recommended. Instead of this, try adding accessibility identifiers to your UI components. This will allow you to refer to them in easy way and you won't have to worry about the fact that someone changed the text displayed in the button. Adding accessibility identifiers to the UI components will make writing your tests much faster, as you will know how to refer to objects instantly.

####3. Look into predefined steps!

After installing Calabash, you should take a look at the predefined steps that are provided by Calabash. They are stored in the installation's directory under `features` folder. This can be really helpful at early stages of your journey. Using these steps you can try to write your own custom steps and use them for your first tests. It is not recommended to use prefedined steps for complex testing, but they can surely give you some hint at the beginning.

*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
