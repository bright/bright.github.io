---
layout: post
title: Custom XCode key bindings  
tags: iOS
comments: true
author: eliasz
---
When I started writing Swift code, I found out that my beloved IDE(AppCode) for Objective-C, was not doing as well as I thought it would. Moreover, I've noticed that XCode did better job in terms of handling CocoaPods, autocompletion, debugging etc. It was more than enough for me to say sorry to XCode and leave AppCode for some time to let it solve it's problems. I really miss it's Objective-C features like code refactoring and it's ability to generate code, but there is no place for sentimentality. Currently I'm proud user of XCode 7.3 beta and I really like it's new autocompletion feature.

After coming back to XCode I found out that I was really used to keybindings that AppCode provided. Some of them were also available in XCode (Moving lines up/down) and it was just a matter of changing the keys binded to a shortcut, but some of them were missing. Where was my line duplication? Where was my lines juming? Luckily for me, I could add those by myself.

####Adding custom keybindings
Thre is an easy way of adding your custom key bindings to your XCode. In order to do this, you have to follow this steps:

1. Go to `/Applications/Xcode.app/Contents/Frameworks/IDEKit.framework/Resources`
2. Find `IDETextKeyBindingSet.plist` file 
3. Edit file
4. Reset your XCode
5. Open `Preferences/KeyBindings` in your XCode
6. Find your custom key bindings and assign keyboard shortcuts for them
7. Start using new shortcuts


How to edit the `IDETextKeyBindingSet.plist` file? If you open it using a normal text editor, you will notice a lot of `<dict>`, `<key>` and `<string>` values. Learn the pattern and add your own key bindings. Here is example of some key bindings that I've added. 
<script src="https://gist.github.com/Eluss/eb7d9290ecddfa758365.js"></script>


*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
