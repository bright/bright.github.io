---
layout: post
title: A fast way to connect to the web proxy
tags: android
comments: true
author: mateuszklimek
---

Configuring Android device to connect to the web proxy running on development build machine is tedious.
You have to make the same steps over and over again on every device you want to debug *HTTP* traffic.<br/ >
It goes like this:<br/>

1. Check development machine *IP* address.
2. Install proxy *CA* certificate on a mobile device in order to spy *HTTPS* traffic.
3. Configure wifi connection on the mobile device to connect through the proxy (by typing it by hand).

Sometimes I have to enable the proxy for a moment and disable it afterward, then... repeat it a couple of times.<br/>
Seems to be pretty good case to automate it :)

I wanted to have a simple switcher which allows quickly enabling or disabling the proxy connection.<br/> 
The solution I made is based on a static global variable. <br/>
{% highlight java %}
public class DevFeatureToggle{
    public static final boolean PROXY_ENABLED = true;
}
{% endhighlight %}

If the flag is set to `true` the app connects to the web proxy automatically.<br/>
You earned a few things here:

* It's transparent for a developer. You don't care about typing *IP* address and port.
* Installs *CA* certificate at runtime. You don't have to install it by hand on the device. You don't even have to have password for credential storage ;)
* Because the device isn't directly connected, the web proxy can see traffic from your app only (other apps' traffic isn't visible).
* You can dynamically enable/disable the proxy for *HTTP* client in runtime so you can pass some requests over the proxy. 
* It's safer. You don't install proxy or any third party certificate on the system directly.

<br/>
# Let's code it.

Add this task on the top of `build.gradle`:

{% highlight groovy %}

ext{
    proxy = 'cannot.get.buildmachine.local.ip'
}

task getBuildMachineLocalIp() {
    def stdout = new ByteArrayOutputStream()
    exec{
        executable "sh"
        args "-c", 'ifconfig en1 | grep "inet " | awk \'{ print $2}\''
        standardOutput = stdout
    }
    project.ext.proxy = stdout.toString().trim()
    println "build machine local ip: " + project.ext.proxy
}
{% endhighlight %}
It gets local *IP* address of your desktop build machine and store it in `project.ext.proxy`.<br/>
Remember to replace `ifconfig` command with proper `ipconfig` if your build machine is running Windows.<br/>
You have to also ensure that `en1` is the interface used to connect to the same wifi network as Android device.

Now, make above task depended on `preBuild` Android's task to run it on the beginning of the build process:

{% highlight groovy %}
android{
    defaultConfig{
        buildConfigField 'String', 'BUILD_MACHINE_LOCAL_IP', '\"' + project.ext.proxy + "\""
    }
}
dependencies{
    ...
}

preBuild.dependsOn getBuildMachineLocalIp

{% endhighlight %}
I made one more thing here. <br/>
Copied value of `project.ext.proxy` to `BuildConfig.BUILD_MACHINE_LOCAL_IP` to have access to *IP* address in code.

The last part is to set up our proxy for *HTTP* client.
{% highlight java %}

private void setProxy(OkHttpClient client) {
    if (FeatureToggle.PROXY_ENABLED && BuildConfig.BUILD_TYPE.equals("debug")) {
        client.setProxy(new Proxy(Proxy.Type.HTTP, new InetSocketAddress(BuildConfig.BUILD_MACHINE_LOCAL_IP, 8888)));
        SSLContext sslContext = SslUtils.getSslContextForCertificateFile(app, "charles-proxy-ssl-proxying-certificate.crt");
        client.setSslSocketFactory(sslContext.getSocketFactory());
        LOG.debug("proxy set for client {}", client);
    }
}

OkHttpClient client = new OkHttpClient();
setProxy(client);

/*
use client as you wish eg. with Retrofit
*/
{% endhighlight %}
There's simple `if` statement which enables proxy when flag is active.<br/>
Probably, you don't want to have the proxy connection for release builds so I also check build type here.<br/>
The last thing is to set up *CA* certificate of the web proxy to make *HTTP* connections visible on it. <br/>
`SslUtils` is simple helper class which I created to trust given certificate despite system settings. <br/>
You can read about `SslUtils` in [this post](http://blog.brightinventions.pl/trust-specific-certificate-on-jvm/) and download it from [GitHub](https://github.com/mklimek/ssl-utils-android).

If you have any tips or thoughts about this method, let me know about it :)

See this post on my [personal blog](https://mklimek.github.io/fast-way-to-connect-web-proxy/).


