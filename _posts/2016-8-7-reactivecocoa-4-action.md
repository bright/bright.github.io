---
layout: post
title: ReactiveCocoa 4 - Action
tags: iOS
comments: true
author: eliasz
---

Today I'll tell you about an `Action` type that is available in ReactiveCocoa 4.

`Action` is pretty straightforward, It takes input, does some work and returns output. Moreover, it can fail and provide us with an `Error` type. Let's look at creation of a simple `action` that takes `Int` as an input and returns `String` or `NSError` if it fails.

{% highlight swift %}
let action = Action<Int, String, NSError>({ (number) -> SignalProducer<String, NSError> in
  return SignalProducer<Void, NSError> {observer, disposable in
    observer.sendNext("Number is \(number)")
    observer.sendCompleted()
  }
})
{% endhighlight %}

Execution
===
How do we execute it? In order to do that we have to perform two steps.

Create Producer
---
{% highlight swift %}
let producer = action.apply(5)
{% endhighlight %}
`Apply` method will return a `SignalProducer` with `Action's` output as value (`String`) and error of `ActionError` type. `ActionError` is an enum that can be either `NotEnabled` or `ProducerError`. `ProducerError` is a wrapper around error type that specify in our `Action`.

Execute
---

{% highlight swift %}
prodcuer.startWithSignal { (signal, disposable ) in
  signal.observeNext({ (value) in
    print("\(value)")
  })
  signal.observeFailed({ (actionError) in
    print("\(actionError)")
  })
}
{% endhighlight %}
Actual execution happens when we start a `Signal` from received `SignalProducer`. If we don't do it, nothing will happen.

Repeating action
---
Remember the `observer.sendCompleted()` part in our action's `SignalProducer`? It is really important, in fact we need to provide any terminating `Event` when we want to finish executing our `Action`. If we do not do that, any `Signal` started from `SignalProducer` will immediately receive `NotEnabled` error and we won't receive any values.

{% highlight swift %}
let action = Action<Int, String, NSError>({ (number) -> SignalProducer<String, NSError> in
  return SignalProducer<String, NSError> {observer, disposable in
    observer.sendNext("Number is \(number)")
    let delayTime = dispatch_time(DISPATCH_TIME_NOW, Int64(1 * Double(NSEC_PER_SEC)))
    dispatch_after(delayTime, dispatch_get_main_queue()) {
      observer.sendCompleted()
    }
  }
})

let prodcuer = action.apply(5)
prodcuer.startWithSignal { (signal, _ ) in
  signal.observeNext({ (value) in
    print("1 - \(value)")
  })

  signal.observeFailed({ (actionError) in
    print("1 - \(actionError)")
  })
}

prodcuer.startWithSignal { (signal, _ ) in
  signal.observeNext({ (value) in
    print("2 - \(value)")
  })

  signal.observeFailed({ (actionError) in
    print("2 - \(actionError)")
  })
}
{% endhighlight %}

Reveived output: "`1 - Number is 5`" and "`2 - NotEnabled`".  
We receive this output because of the fact that when we try to execute our action second time, our first execution hasn't completed yet.

Observing Action
===
Beside observing single execution of an `Action`, you can also observe `Action` itself and you'll receive values that come from each execution. Prepare for a fun part now. There are three ways to observe `Action's` values. You can observe `Action's` values, `Action's` errors and `Action's` events.

What's the difference? Let's have a look.

Action's values
---
{% highlight swift %}
let action = Action<Int, String, NSError>({ (number) -> SignalProducer<String, NSError> in
    return SignalProducer<String, NSError> {observer, disposable in
        observer.sendNext("Number is \(number)")
        let delayTime = dispatch_time(DISPATCH_TIME_NOW, Int64(1 * Double(NSEC_PER_SEC)))
        dispatch_after(delayTime, dispatch_get_main_queue()) {
            observer.sendInterrupted()
        }
    }
})

action.values.observe { (event) in
  print("Value: \(event)")
}

action.apply(5).startWithSignal { (_ , _ ) in }
{% endhighlight %}
Output: "`Value: Next Number is 5`" and "`Value: COMPLETED`"  
Our values signal is of type `Signal<Output, NoError>`, so you won't get any errors here. If any terminating `Event` occurs during `Action's` execution, you'll receive `COMPLETED` event.

Action's errors
---
{% highlight swift %}
let action = Action<Int, String, NSError>({ (number) -> SignalProducer<String, NSError> in
    return SignalProducer<String, NSError> {observer, disposable in
        observer.sendNext("Number is \(number)")
        let delayTime = dispatch_time(DISPATCH_TIME_NOW, Int64(1 * Double(NSEC_PER_SEC)))
        dispatch_after(delayTime, dispatch_get_main_queue()) {
            observer.sendInterrupted()
        }
    }
})

action.errors.observe { (event) in
  print("Error: \(event)")
}

action.apply(5).startWithSignal { (_ , _ ) in }
{% endhighlight %}

Output: "`Error: Next Error Domain=1 Code=1 "(null)"`" and "`Error: COMPLETED`"  
Errors' signal is of type `Signal<Error, NoError>` so you can focus on observing errors that occur during `Action's` execution. Keep in mind, that you don't use `observeFailed` method to observe errors, as they come with `NEXT` events. After `Action's` execution is finished, signal will receive `COMPLETED` event.

Action's events
---
{% highlight swift %}
let action = Action<Int, String, NSError>({ (number) -> SignalProducer<String, NSError> in
    return SignalProducer<String, NSError> {observer, disposable in
        observer.sendNext("Number is \(number)")
        let delayTime = dispatch_time(DISPATCH_TIME_NOW, Int64(1 * Double(NSEC_PER_SEC)))
        dispatch_after(delayTime, dispatch_get_main_queue()) {
            observer.sendFailed(NSError(domain: "1", code: 1, userInfo: nil))
        }
    }
})

action.events.observe { (event) in
  print("Event: \(event)")
}

action.apply(5).startWithSignal { (_ , _ ) in }
{% endhighlight %}
Output: "`Event: NEXT NEXT Number is 5`", "`Event: NEXT FAILED Error Domain=1 Code=1 ("null")`" and "`Event: COMPLETED`"  
Third option is to observe signal of ALL `events`. This signal is of type `Signal<Event<Output, Error>, NoError>`. It means, that you will receive ALL `events` as next values, even terminating.  
I have to say that this output is a bit confusing. What is "`Event: NEXT NEXT Number is 5`"? Why do we get double `NEXT` here? Let's go step by step here.
Let's look at `Action's` `SignalProducer` implementation. First thing that we send is `observer.sendNext("Number is \(number)")` It will send a `NEXT` event with a `String` value.
Our event's observer receives `Next` events with `Next` event from our action. That's why we get "`NEXT NEXT`". Next event that is sent is "`observer.sendFailed(NSError(domain: "1", code: 1, userInfo: nil))`". As previously, our `Failed` event comes to `event's` observer as `Next` event. That is why we get "`NEXT FAILED`". After this terminating event, a `COMPLETED` event is sent to our `event's` observer indicating that execution has finished.

I hope that this made `Actions` clearer to you! See you next time.

*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
