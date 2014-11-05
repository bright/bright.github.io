---
layout: post
excerpt: "I’m experimenting with ShareJS library, which is intended to allow live concurrent editing like in Google Docs. The demo on their website seems incredibly easy, even though later on the page they are so cruel: “ShareJS is mostly working, but it’s still a bit shit.”. I wouldn’t be so harsh as I was able to have it up and running in less than few hours. But the fact is it wasn’t as easy as it seemed."
title: ShareJS 0.7.3 working example
modified: 2014-10-30
tags: [sharejs, nodejs, javascript]
comments: true
author: adam
---

I’m experimenting with [ShareJS](http://sharejs.org/) library, which is intended to allow live concurrent editing like in Google Docs. The demo on their website seems incredibly easy, even though later on the page they are so cruel: “*ShareJS is mostly working, but it’s still a bit shit.*”. I wouldn’t be so harsh as I was able to have it up and running in less than few hours. But the fact is it wasn’t as easy as it seemed.

It looks like the main problem with current state of ShareJS is what is pretty common in wild and uncontrolled open source world - lack of proper documentation. Here the problem is even worse. There are [some docs](https://github.com/share/ShareJS/wiki) and [examples](http://sharejs.org/demos.html), but most of it is either incomplete or outdated. ShareJS.org website runs on ShareJS 0.5, while the most recent release is 0.7.3, with no backward compatibility between those releases. I think it will be less harmful if there was no examples at all - right now they are more misleading than helpful. It was a bit frustrating when even the shortest and simplest snippet from their website didn’t work, failing on non-existing functions being called.

Anyway, I was able to figure out what I need to change to have the simple demo running, both server- and client-side. Here it is, in case you have the same struggle, too.

On **server-side**, I’m running CoffeeScript WebSocket server, almost like in [the original sample](https://github.com/share/ShareJS/blob/master/examples/ws.coffee). I just needed few changes in order to have it running with [Connect 3](https://github.com/senchalabs/connect#readme) - logging and static serving middlewares are no longer included in Connect out of the box, so I used [`morgan`](https://github.com/expressjs/morgan) and [`serve-static`](https://github.com/expressjs/serve-static), respectively. Here is the only changed part around Connect middlewares initialization:

{% highlight CoffeeScript %}
app = connect()
app.use morgan()
app.use '/srv', serveStatic sharejs.scriptsDir
app.use serveStatic "#{__dirname}/app”
{% endhighlight %}

Go here for full Gist: [ShareJS 0.7.3 server-side code](https://gist.github.com/NOtherDev/f288b939d19499060e1b).

I’m exposing client JavaScript libraries provided with ShareJS under `/srv` path and the client-facing web application files, physically located in `/app` on my filesystem, are exposed directly in the root path.

**Client-side** was a bit harder. Running the original code from the main ShareJS.org website wasn’t successful.

{% highlight JavaScript %}
sharejs.open('blag', 'text', function(error, doc) {
  var elem = document.getElementById('pad');
  doc.attach_textarea(elem);
});
{% endhighlight %}

It tries to call `sharejs.open` function, which yields “`TypeError: undefined is not a function`” error for a simple reason - there is no longer “`open`” function on `sharejs` global variable. Fiddling around, I found an example that is using more verbose call like this:

{% highlight JavaScript %}
var ws = new WebSocket('ws://127.0.0.1:7007');
var share = new sharejs.Connection(ws);
var doc = share.get('blag', 'doc');

if (!doc.type) {
    doc.create('text');
}

doc.whenReady(function () {
    var elem = document.getElementById('pad');
    doc.attachTextarea(elem);
});
{% endhighlight %}

Seemed legitimate and didn’t fail immediately, but I was getting "`Operation was rejected (Document already exists). Trying to rollback change locally.`” error message anytime except the first time. The code was calling `doc.create('text')` every time and that was clearly wrong, I should get `doc.type` pre-populated somehow. The solution is to subscribe to the document first and move checking the type and creating when needed to the function called after the document is ready - like this:

{% highlight JavaScript %}
var ws = new WebSocket('ws://127.0.0.1:7007');
var share = new sharejs.Connection(ws);
var doc = share.get('blag', 'doc');
doc.subscribe();

doc.whenReady(function () {
    if (!doc.type) {
        doc.create('text');
    }

    var elem = document.getElementById('pad');
    doc.attachTextarea(elem);
});
{% endhighlight %}

See the full Gist: [ShareJS 0.7.3 client-side code](https://gist.github.com/NOtherDev/2ea2bb111c00282e7617).

*This post is cross-posted with [my personal blog](http://notherdev.blogspot.com/2014/10/sharejs-073-working-example.html).*
