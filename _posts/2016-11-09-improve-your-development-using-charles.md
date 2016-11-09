---
layout: post
title: Make your development better. Use the proxy.
author: kwysocki
tags : [iOS, swift, proxy, web]
comments: true
---

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/head.jpeg?raw=true)

In this post, I would like to describe you to set up a proxy using Charles desktop app. I believe that many of you work with API or consume some REST Service. If haven't heard about proxy yet I believe the knowledge from this post will be useful in your future development. 
The following example concerns an iOS environment and configuring at the OSX system. 

# What the proxy is?

To tell you what the Proxy is I use the definition that I found in Charles [documentation](https://www.charlesproxy.com/documentation/additional/http-proxy/)
>An HTTP Proxy is a server that receives requests from your web browser and then makes the request to the Internet on your behalf. It then returns the results to your browser.

So the Charles app is kind of monitor that inspects your network traffic, does all requests on your behalf and returns response back to you.

# Do I really need it?

Yes! It might help you when you're creating an app that consumes some API. You'll be able to look throught the request and response from the server. Also, Charles app allows you to set a break point for endpoint and gives you ability to edit a request or response body, so you can test a various scenarios for your app. Likewise you can see how many request your app really does.

# How to configure Charles?

First, go to [Charles website](https://www.charlesproxy.com/download/) and download the installation file. After the installation process you will see the main screen of the app. At start I recommend to select the `Sequence` tab on the top. 

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/main_screen.png?raw=true)

After some time you should see all request that you do on your mac.
Now, we have two ways to configure the Charles. First way is to configure it for iOS Simulator. The second option is configure Charles for iOS device.

***iOS Simluator configuration***

Click on Help -> SSL Proxying -> Install Charles Root Certificate in iOS Simualator.

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/install_on_ios.png?raw=true)

You will see the prompt:


![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/prompt.png?raw=true)


Then click OK and for sure you should restart Simulator. After that steps Charles is configured and ready to work with your Simulator.

***iOS Device configuration***

On your device in Wi-Fi connection settings choose the same connection that your Mac using, tap on it. Then swipe down, and choose Proxy Setting to Manual.

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/proxy_iphone.png?raw=true)

In the IP field please put the same IP address that your Mac Wi-Fi uses. In the `port` field type `8888`. 

To see IP-address of your Wi-Fi on your Mac:
 
Right-click with option button on your wifi icon on the Mac

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/wifi_mac.png?raw=true)

Then on your iOS device go to [http://www.charlesproxy.com/getssl/](http://www.charlesproxy.com/getssl/) and install the certificate. I recommend to do it via Safari because it redirects you from URL to Certificates Settings on iOS Device. Then just install the certificate.

After the installation process, you will be able to see all request from your iOS device in Charles app on your Mac.

# Ok, all configured but, how to use it?

After the configuration flow, you will see the Charles main window, and your network data. For the purposes of this post I wrote a simple app that consume free rest API `https://www.freegeoip.net/`. This API gives us some geo-information about any domain. For example:  
https://www.freegeoip.net/json/github.com returns geo-information about `github.com` site.

Now, let's call `https://www.freegeoip.net/json/twitter.com` from iOS Simulator/iPhone. On Charles main window, we're able too see the request was made and we received a response. There is no information about request parameters, response code, etc.

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/call.png?raw=true)

Let's take a look on that.

***Overview tab***

In the `Overview` tab we can see information about Protocol used to this call, SSL, also you're able too see the request time and size of request and response. 

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/overview.png?raw=true)

Ok, let's have look on the request and response

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/request_non_readable.png?raw=true)

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/response_non_readable.png?raw=true)

Unfortunately, both are not readable for Charles. It's all because the request is secure and uses the `https`. So, we need to inform Charles about that this domain is using the `SSL` to see the request details.

***Adding Proxy SSL***

There're two ways to do that. 

First you can click on the `Proxy settings`, then click `Add` the append host to the list. In the `Host` field type the host address of your server. In my example that would be `www.freegeoip.net`. 

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/add_proxy_settings.png?raw=true)

In the port field type `443`. Why `443` ? Because the `443` is the default port HTTPS connections use.

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/host_and_port.png?raw=true)

Make sure that you have `Enable Proxy SSL` checkbox selected.
After adding the host it should look like that : 

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/after_adding.png?raw=true)


Second way is much simpler, just right-click on the request and select the `Enable SSL Proxying`.

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/enable_proxy_settings.png?raw=true)

***Request using SSL Proxying***

After adding the SSL Proxying for our host server, perform request again.

![](/assets/posts/charles/ssl_request_screen.png)

As you can see right now we're able to see the `RC`(response code) of the request. Here it is 200(OK). Also we know that our request was `GET` type. Let's take a look at the request and response body.

In the request body, we have some information about Request type, language, and encoding. Also, you can find here some information about Cookies.

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/ssl_request_screen.png?raw=true)

The response body becomes readable from Charles. After choosing the `JSON text` tab we can see formatted JSON string. All informations about the response are stored in the `RAW` tab. You can find here information about Response code, Content-Type, Server, encoding and many others.	

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/charles_tab.png?raw=true)

And now the most important - response body:

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/response_body_ssl.png?raw=true)

# Debugging

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/havefun_mem.jpg?raw=true)

The cool thing about Charles is that the app allows you to debug the requests/responses in the same way as XCode and many others IDEs breakpoints work. But here you put the breakepoint on the request, not on the line of code.

To set a breakpoint, just right-click on the line with the request that you want to debug and select `Breakpoints` option.
 
![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/breakpoint.png?raw=true)

Now, make the request again and you should see the Breakpoint-Window.
 
With the `Overview` and `Edit Request` tabs. Here you're able to edit the request body, query string parameters, request method, cookies, headers or even choose the HTTP version. After editing the request just click `Execute` on the bottom.

After executing when the Charles receives the response we should see the Breakpoint-Window again and now we're ready to edit the response! 

![](https://github.com/k8mil/k8mil.github.io/blob/master/assets/posts/charles/edit_response.png?raw=true)

By editing the response I mean that you can edit Response Headers and Response Body. Also you can check the body and the overview of the request. After editing the response, just click the Execute button.
After that steps, the edited response goes to our device/simulator. 

>Note: Remember to set the breakpoint off, when you don't need it anymore because if you don't this the breakpoint will run every time that request was triggered. 


# Conclusion

Charles app is a great tool and has many many features. In my post I focused on the iOS environment, but you can also configure the proxy for your Android Emulator or Android device.
I described most common usages which I do with Charles app. To sum up I think that app should take a place in your development tools directory if you work with some web server.


*This post is cross-posted with my personal [blog](https://wysockikamil.com/improve-your-development-using-charles/)*



