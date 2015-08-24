---
layout: post
title: 'Simplistic JavaScript dependency injection with ES6 destructuring'
date: 2015-08-24
tags: [JavaScript, ES6]
comments: true
author: adam
excerpt: "Recently I got a bit tired with Angular's quirks and intricacies. To freshen up, I'm playing with framework-less JavaScript (Vanilla JS). I'm also getting more and more used to ES6 features. One of the outcomes by now is the idea for the Dependency Injection approach that stays simplistic, decoupled from any framework and still convenient to consume."
---

Recently I got a bit tired with Angular's quirks and intricacies. To freshen up, I'm playing with framework-less JavaScript ([Vanilla JS](http://vanilla-js.com/)). I'm also getting more and more used to ES6 features. One of the outcomes by now is the idea for the [Dependency Injection](http://www.martinfowler.com/articles/injection.html) approach that stays simplistic, decoupled from any framework and still convenient to consume.

## Destructuring

One of the features I like most in ES6 is [destructuring](http://www.2ality.com/2015/01/es6-destructuring.html). It introduces a convenient syntax for getting multiple values from arrays or objects in a single step, i.e. do the following:

{% highlight JavaScript %}
let [lat, lng] = [54.4049, 18.5763];
console.log(lat); // 54.4049
console.log(lng); // 18.5763
{% endhighlight %}

or like this:

{% highlight JavaScript %}
let source = { first: 1, second: 2 };
let { first, second } = source;
console.log(first, second); // 1, 2
{% endhighlight %}

What is even nicer, it works fine in a function definition, too, making it a great replacement for the [config object pattern](http://christianheilmann.com/2008/05/23/script-configuration/), where instead of providing the large number of parameters, some of them potentially optional, we provide a single plain configuration object and read all the relevant options from the inside the object provided. So, with ES6 destructuring (+default parameters support), instead of this:

{% highlight JavaScript %}
function configurable(config) {
    var option1 = config.option1 || 123;
    var option2 = config.option2 || 'abc';
    // the actual code starts here...
}
{% endhighlight %}

we can move all that read-config-and-apply-default-if-needed stuff directly as a parameter:

{% highlight JavaScript %}
function configurable({ option1 = 123, option2 = 'abc' }) {
    // the actual code from the very beginning...
}
{% endhighlight %}

The code is equivalent and the change doesn't require any changes at the caller side.

# Injecting

We can use destructuring to provide Angular-like experience for receiving the dependencies by a class or a function that is even more cruft-free as it's minification-safe and thus doesn't require tricks like [ngAnnotate](https://github.com/olov/ng-annotate) does. 

Here is how it can look from the dependencies consumer side:

{% highlight JavaScript %}
function iHaveDependencies({ dependency1, dependency2 }) {
    // use dependency1 & dependency2
}
{% endhighlight %}

Whenever we invoke the `iHaveDependencies` function, we need to pass it a single parameter containing the object with `dependency1` and `dependency2` keys, but possibly also with others. Nothing prevents us from passing the object with all the possible dependencies there (a container).

So the last thing is to ensure we have one available whenever we create the objects (or invoke the functions):

{% highlight JavaScript %}
// possibly create it once and keep it for a long time
let container = { 
  dependency1: createDependency1(),
  dependency2: createDependency2(),
  dependency3: createDependency3(),
  otherDependency: createOtherDependency() 
};

// use our "container" to resolve dependencies
iHaveDependencies(container);
{% endhighlight %}

That's all. The destructuring mechanism will take care of populating `dependency1` and `dependency2` variables within our function seamlessly. 

We can easily build a dependency injection "framework" on top of that. The implementation would vary depending on how our application creates and accesses the objects, but the general idea will hold true. Isn't that neat?

PS. Because the lack of direct ES6 support in the browsers, running that in a browser right now requires transpiling it down to ES5 beforehand. [Babel](https://babeljs.io/) works great for that.
