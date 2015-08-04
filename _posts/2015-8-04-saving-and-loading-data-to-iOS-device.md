---
layout: post
title: NSKeyedArchiver/Unarchiver
tags: iOS
comments: true
author: eliasz
---
Lately I've spent some time wrtiting an app for myself. It is supposed to let you create tasks, mark them as done/undone and then track your progress. I've called it "Habit Tracker" and it is available
<a href="https://github.com/Eluss/HabitTracker" target="_blank">here</a>. While writing this utility I came across a few interesting issues and this blogpost will cover one of them.

###Saving and Loading data

The feature that "Habit tracker" surely needed was ability to store and load users tasks. Core data seemed a bit overkill for me, so I decided to use NSKeyedArchiver/Unarchiver.

####NSCoding
First of all lets create a class which we want to archive. It will be called Task. In order to save and load it with use of NSKeyedArchiver/Unarchiver we have to make our class implements two methods provided by protocol \<NSCoding\>.
<script src="https://gist.github.com/Eluss/fb436b922e73aa7e0f50.js"></script>

Now lets create our example Task class.

<script src="https://gist.github.com/Eluss/2cd388a213795b949403.js"></script>

<script src="https://gist.github.com/Eluss/0ee3bc08b6805c36c61d.js"></script>

Now we are able to properly store our tasks in files.

####Saving
<script src="https://gist.github.com/Eluss/ad7b1e1d00f9a49f8978.js"></script>

####Loading
<script src="https://gist.github.com/Eluss/565c38f567ab5482202b.js"></script>

If we use iOS Simulator, all files that are saved by us are stored in our app's folder. Using my saving code you will find file named "FILE_NAME" under:

/Library/Developer/CoreSimulator/Devices/{DEVICE_ID}/data/Containers/Data/Application/{APP_ID}/Documents

By using this command you can get your currently running iOS Simulator ID:
<script src="https://gist.github.com/Eluss/d10d6e783bd5c17dfee3.js"></script>

This article is cross-posted with my [my personal blog](http://eluss.github.io/Savings-and-loading-data-to-iOS-device/)
