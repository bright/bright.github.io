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

<br/>
# Warning: Copy and Paste

{% highlight java %}
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.FileInputStream;
import java.io.InputStream;
import java.security.KeyStore;
import java.security.SecureRandom;
import java.security.cert.Certificate;
import java.security.cert.CertificateFactory;
import java.security.cert.X509Certificate;

import javax.net.ssl.SSLContext;
import javax.net.ssl.TrustManagerFactory;

public class SslUtils {

    private static final Logger LOG = LoggerFactory.getLogger(SslUtils.class.getSimpleName());

    public static SSLContext getSslContextForCertificateFile(String fileName) {
        try {
            KeyStore keyStore = SslUtils.getKeyStore(fileName);
            SSLContext sslContext = SSLContext.getInstance("SSL");
            TrustManagerFactory trustManagerFactory = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
            trustManagerFactory.init(keyStore);
            sslContext.init(null, trustManagerFactory.getTrustManagers(), new SecureRandom());
            return sslContext;
        } catch (Exception e) {
            String msg = "Cannot load certificate from file";
            LOG.error(msg, e);
            throw new RuntimeException(msg);
        }
    }

    private static KeyStore getKeyStore(String fileName) {
        KeyStore keyStore = null;
        try {
            CertificateFactory cf = CertificateFactory.getInstance("X.509");
            InputStream inputStream = new FileInputStream(fileName);
            Certificate ca;
            try {
                ca = cf.generateCertificate(inputStream);
                LOG.debug("ca={}", ((X509Certificate) ca).getSubjectDN());
            } finally {
                inputStream.close();
            }

            String keyStoreType = KeyStore.getDefaultType();
            keyStore = KeyStore.getInstance(keyStoreType);
            keyStore.load(null, null);
            keyStore.setCertificateEntry("ca", ca);
        } catch (Exception e) {
            LOG.error("Error during getting keystore", e);
        }
        return keyStore;
    }
}
{% endhighlight %}

#Example usage

{% highlight java %}
OkHttpClient client = new OkHttpClient();
SSLContext sslContext = SslUtils.getSslContextForCertificateFile("BPClass2RootCA-sha2.cer");
client.setSslSocketFactory(sslContext.getSocketFactory());
{% endhighlight %}
You can easily adopt that code in any JVM language like Groovy, Kotlin, etc.<br/>
Available as gist [here](https://gist.github.com/mklimek/f9d197362c1f2db8c1b76f76ace75859).<br/>
Version for Android to load certificate from assets is [here](https://gist.github.com/mklimek/4a344b606d96c5200334bf2f48c174d6).


See this post on my [personal blog](https://mklimek.github.io/trust-specific-certificate-on-jvm/).



