---
layout: post
excerpt: "Meet Injection Helper, a small utility for making InjectionForXcode more friendly to use. Get rid of superfluous messages and a flood of XCode windows opened any time you patch/revert your project."
title: InjectionForXcode meets command line
modified: 2014-11-07
tags: [ios, appcode, xcode, injectionforxcode, gem]
comments: true
author: michal
---

I find [InjectionForXcode](http://injectionforxcode.com/) to be an indispensable tool in my daily iOS development. It works brilliantly but generates a lot of noise that I just couldn’t cope with.

I use AppCode. Whenever I execute `Run -> Patch Project For Injection` there’s a flood of messages telling me about changes in each individual `prefix.pch` file. There’s one prefix file for each CocoaPod in the project so you can imagine the pain of having to press “OK” button in a dozen of alert messages. Additionally after each file has been modified Xcode opens and shows the updated code. Yuck. You can live with this if you patch project for injection once and then keep patched project in your repository. The thing is that I don’t want to keep patched project in the repository. I don’t want to force InjectionForXcode onto other people working on the code. I want to quickly patch the project, do what I need to do and then revert the project to its previous state.

There’s also one other problem. I really hate the alert message that appears in the running app every time some code has been injected. I know that injection takes place because I initiate it. I immediately see the change in the app, I don’t need another alert message confirming it. After all I can always use [Blinker](https://github.com/bright/blinker) to indicate a change.

So many issues with such a great tool. Happily I managed to create a small gem that makes it all easier. It’s called [Injection Helper](https://github.com/bright/injection-helper) and you can install it by executing:

{% highlight Bash %}
gem install injection-helper
{% endhighlight %}


Here’s what you can do with it.

### Turn on mute mode for InjectionForXcode

Just execute `injection mute` from your project directory and you won’t see any more of those pesky messages.  There will also be no Xcode windows opened when you patch project for injection (or revert injection code) next time.

You can always unmute it by executing `injection unmute`.

### Patch project for injection from command line

It’s easier and feels better to patch project for injection straight from your command line.
Just go to your project directory and execute: `injection patch` to patch project or `injection revert` to remove all the boilerplate code.

There’s also `injection refresh` command that reverts injection changes and then patches the project again. It’s great if you run your app on a real device and don’t want to change your ip address in `main.m` every time you switch your wifi.

