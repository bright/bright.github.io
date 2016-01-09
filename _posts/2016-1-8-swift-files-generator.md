---
layout: post
title: Swift files generator
tags: iOS
comments: true
author: eliasz
---

For some time, I have been creating iOS apps without using storyboards at all. Because of this fact ,creating screens in the application is connected to some repetitive steps. You create a ViewController, then a View which will be presented in the controller. You want a PageViewController? Create one, set up ViewControllers that will be presented inside it. After some time, you can recognise a pattern and prepare a bunch of code snippets that will do the job for you. Or... You can prepare a code generator, that will generate all the files for you. This will allow you to skip the part of creating files and filling them with code templates. In this blog post, I will tell you about creating my first code generator.

####What should it generate?
So... I've decided to create my first code generator. The first question is: "What should it generate?". For me the answer was simple. For some time I've been using the same code snippets, and I knew that I could use the same idea for my generator. I wanted to make a generator, that could create a structure which would be added to project and then filled up why my code. 

####How to build it?
Second question is: "How to build it?". Some time ago I've wrote basic bash scripts and I knew I could use this knowledge to write this generator. This time it is bash, but I think that if you do this kind of stuff, it is a great opportunity to learn some new languages.

####How should it work?
Last question: "How should it work?". I wanted my script to read a file and build a structure based on it. The file structure had to be easy to write, so that I would use the tool instead of using the code snippets. After writing the input file, you simply run the script and everything is created for you. It's enough for me to start using my new tool, leaving old repetitive way behind.

If you have got answer for these questions it is time for you to sit down and start creating your tool. For me it was great fun and I encourage you to create your generator, as it can be a great way of learning new things.

You can find my generator on [my Github](https://github.com/Eluss/SwiftFilesGenerator).


*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*