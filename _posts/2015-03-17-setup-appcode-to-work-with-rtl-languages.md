---
layout: post
excerpt: ""
title: "Setup AppCode to start Simulator in RTL mode"
modified: 2015-03-17
tags: [iOS]
comments: true
author: mateuszklimek
---
# Question
How make iOS Simulator get to work with Right-To-Left languages when it's stared from AppCode?
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
Keep in mind if you kill app and start it again in simulator, parameters **don't be** included for new process. Thus app will start in common LTR mode.
The only way to restart app in RTL is run app from AppCode again.



