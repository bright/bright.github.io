---
layout: post
title: Trust specific certificate on JVM-based platforms
tags: jvm java android
comments: true
author: mateuszklimek
---

I wrote simple helper which allow loading specific certificate to [SSLContext](https://docs.oracle.com/javase/7/docs/api/javax/net/ssl/SSLContext.html).<br/>
You can use it to support *HTTPS* connections which rely on a untrusted certificate. <br/>
By untrusted certificate, I mean this one which server is certified but system denies it (doesn't trust it) for some reason.<br/>
I found it very useful to load particular certificate dynamically.

For example:

1. Older Android devices don't support some new CA providers. If you want to ship an app with support to such CA and don't want to force a user to install it himself you can add that CA to the app at runtime. Totally transparent to the user.
2. Security reasons. No need to install third party certs on the system directly. Eg. during development phase server might be certified by temporarily `ssh-development-only-certificate.cer`. No one should trust it except development-phase client app.
The second case: you want to use the web proxy. It's also risky to install proxy certificate for the whole system.
3. You have no rights to add proper CA to the system. You told about it your administrator but you're still waiting or worse, he refuses.


# Warning: Copy and Paste

<script src="https://gist.github.com/mklimek/f9d197362c1f2db8c1b76f76ace75859.js"></script>

#Example usage

<script src="https://gist.github.com/mklimek/a623509f2c0a99096a4bc0bd71f7bf62.js"></script>

You can easily adopt that code in any JVM language like Groovy, Kotlin, etc.<br/>
On Android you can load certificate from assets. Github repo is [here](https://github.com/mklimek/ssl-utils-android).


See this post on my [personal blog](https://mklimek.github.io/trust-specific-certificate-on-jvm/).



