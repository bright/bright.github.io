---
layout: post
excerpt: "One thing that I found missing in ShareJS library was the possibility to attach live concurrent editing to HTML &lt;select&gt; element. Out of the box it works only with text fields - &lt;input&gt; and &lt;textarea&gt;. Here is the workaround."
title: Attaching ShareJS to select HTML element
modified: 2014-11-05
tags: [sharejs, javascript]
comments: true
author: adam
---

One thing that I found missing in [ShareJS](http://sharejs.org/) library was the possibility to attach live concurrent editing to HTML `<select>` element. Out of the box it works only with text fields - `<input>` and `<textarea>` using `doc.attachTextarea(elem)` function.

Working around that deficiency wasn't so trivial. ShareJS works with [Operational Transformations](http://en.wikipedia.org/wiki/Operational_transformation) that extracts each logical change to the text (addition or removal) and sends only the change information over the wire. It is great for textual elements, but for `<select>`, whose value is replaced always in one shot, it makes a little sense.

Unfortunately, there is no "replace" operation we could use on `<select>` value change - the *modus operandi* we have to live with is constrained to insertions and removals. It means we have to mimic "replace" operation with removal and insertion. The problem with this approach is that when the operations get reversed - so that the client receives new value insertion first and then removal of the previous value - the intermittent value in-between is not a valid `<option>`. It is a concatenation of old value and new value. DOM API doesn't like that and rejects that change, setting the `<select>` value to empty one. The removal operation that comes next is then unable to fix the value as it tries to remove something from already empty string in DOM.

I have worked around that wrapping my DOM element with a tiny wrapper that keeps the raw value and exposes it for ShareJS transformations while still trying to update the original element's DOM:

{% highlight JavaScript %}
var rawValue = innerElem.value;
var elem = {
    get value () {
        return rawValue;
    },
    set value (v) {
        rawValue = v;
        innerElem.value = v;
    }
};
{% endhighlight %}

ShareJS also doesn't attach itself to `change` event, typical for `<select>` element - it specializes in keyboard events. So I have to attach on my own and rely the event to the underlying ShareJS implementation, faking the event of type that is handled by the library - I've chosen the mysterious `textInput` event.

Here is the full code as Gist: [ShareJS attachSelect](https://gist.github.com/NOtherDev/9e713cfd68d6da9a174a). It adds a new function to the `Doc` prototype, allowing calling it in the same way we're calling ShareJS native `attachTextarea`:  

{% highlight JavaScript %}
if (elem.tagName.toLowerCase() === 'select') {
    doc.attachSelect(elem);
} else {
    doc.attachTextarea(elem);
}
{% endhighlight %}

Feel free to use the code, I hope someone finds that useful.
