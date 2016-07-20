---
layout: post
title: ReactiveCocoa 4 - Events
tags: iOS
comments: true
author: eliasz
---

Understanding signal events in ReactiveCocoa is a must. We can't effectively use signals and signal producers if we don't know what will happen after certain event is received. We distinguish two kinds of events that you can send through signals - terminating and non-terminating. There are three kinds of terminating events: `Failed`, `Interrupted`, `Completed`, and one non-terminating - `Next`.

Signals and events
---
You can either create a pipe with `Signal.pipe()` that will return signal and observer, or you can create a signal with a closure, inside which you will have access to an observer. Signal is controlled by sending events to given observer. You can subscribe to signal multiple times, and each subscription means, that from this moment you are interested in events that will flow down the stream. However, you will not receive events that were sent before you subscribed. When a terminating event is sent to observer, each signal subscriber is informed about it and observer is disposed. From now on, sending events to observer will take no effect, and each new signal subscriber will receive `Interrupted` event.

Example:
{% highlight swift %}
let (signal, observer) = Signal<Int, NoError>.pipe()

observer.sendNext(1)
observer.sendNext(2)

signal.observeNext({ (next) in
    print("observer-1  value: \(next)")
})

signal.observeNext({ (next) in
    print("observer-2 value: \(next)")
})

signal.observeCompleted({
    print("completed")
})

signal.observeInterrupted({
    print("interrupted-1")
})

observer.sendNext(4)
observer.sendCompleted()


signal.observeNext({ (next) in
    print(next)
})
signal.observeCompleted({
    print("completed")
})

signal.observeInterrupted({
    print("interrupted-2")
})

signal.observeInterrupted({
    print("interrupted-3")
})

observer.sendNext(4)
observer.sendCompleted()

{% endhighlight %}
In this case, the output will be - `observer-1 value: 4, observer-2 value: 4, completed, interrupted-2, interrupted-3`. You can see here that we don't receive events before we start observing signal - we miss values `1` and `2`. Then, after terminating event is received (`completed`), we stop receiving any further events and sending values to observer doesn't take effect. However, if we start observing for `interrupted` event after signal was terminated, we will receive it immediately.


Signal producers and events
---
There are two ways of creating a signal producer that will provide us with a different overall result. You can either create a producer with a closure that will be invoked for each invocation of start(), this is a good method to receive events from tasks (eg. network request).


On the other hand, you can create a producer with a buffered observer. Buffered observer is a special kind of observer that will remember sent values up to given buffer capacity. However, this does not include terminating events, which are stored separately. From now on, you can send values to buffered observer, and each time you start a signal from a producer, you will first receive values stored in a buffer and then terminating event (if such has been sent to observer before). If there was no terminating event, subscriber of created signal will receive events that are sent to buffered observer. If you send a terminating event to a buffered observer, then all signals started from that signal producer will receive this event and will behave like normal signals (They won't receive any further events). Buffered observer can receive terminating events multiple times, each terminating event will override previous one.

Example:
{% highlight swift %}
let (producer, observer) = SignalProducer<Int, NoError>.buffer(3)

observer.sendNext(1)
observer.sendNext(2)
observer.sendNext(3)
observer.sendCompleted()

producer.startWithSignal({ (signal, disposable) in
    signal.observeNext({ (next) in
        print(next)
    })
    signal.observeCompleted({
        print("completed")
    })

    signal.observeInterrupted({
        print("interrupted")
    })
})

observer.sendNext(4)
observer.sendInterrupted()

producer.startWithSignal({ (signal, disposable) in
    signal.observeNext({ (next) in
        print(next)
    })
    signal.observeCompleted({
        print("completed")
    })

    signal.observeInterrupted({
        print("interrupted")
    })
})
{% endhighlight %}

In this case, the output will be - `1, 2, 3, completed, 2, 3, 4, interrupted`. You can see here that `interrupted` event overrides `completed`, and output is different next time that we start a signal from a producer.


*This article is cross-posted with my [my personal blog](http://eluss.github.io/).*
