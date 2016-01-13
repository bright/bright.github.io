---
layout: post
excerpt: "How to make iOS Simulator work with Right-To-Left languages when it's started from AppCode?"
title: "Setup AppCode to start Simulator in RTL mode"
modified: 2015-03-17
tags: [iOS]
comments: true
author: mateuszklimek
---
# Question
How to make iOS Simulator work with Right-To-Left languages when it's started from AppCode?
# Answer
Paste these two parameters:
{% highlight xml %}
-AppleTextDirection YES 
-NSForceRightToLeftWritingDirection YES
{% endhighlight %}
into `program arguments' in Run/Debug Configuration.
<br/><br/>
![appcode-rtl-config]({{site.url}}/images/appcode-rtl-config.png)
<br/><br/>
Keep in mind that if you kill the app and start it again in simulator, parameters **wouldn't be** included for the new process. Thus the app will start in the common LTR mode.
The only way to restart the app in RTL is to run the app from AppCode again.


See this post on my [personal blog](http://mklimek.github.io/setup-appcode-to-work-with-rtl-languages/).
