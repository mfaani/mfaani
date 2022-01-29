---
title: "Should I Always Use Barrier Flag?"
date: 2022-01-25T17:04:59-05:00
draft: true
---

DispatchQueues have this flag named `barrier`. You use on your _concurrent_ queues to block other concurrent queues to execute simultaneously. [Docs](https://developer.apple.com/documentation/dispatch/dispatchworkitemflags/1780674-barrier) say:

> Cause the work item to act as a barrier block when submitted to a concurrent queue.

This got me thinking, so if `.barrier` flags are so cool, then why not use them every time we have a read-write problem.

I asked this from a senior engineer. The answer I got was quite interesting.

tl;dr serial queues with sync dispatch are better for most cases. Only use concurrency for when the actual task takes more than `1ms`. Because **creating new threads actually takes time itself**

Generally you'd use a concurrent queue + barriers, but unfortunately there’s two non-inherent reasons why you might not want to in practice:

## Concurrency Cost

Nothing good is free!

Additional concurrency can be surprisingly harmful to the performance of everything else around it (the OS, the rest of your app, other apps, memory usage, etc…)


The "classic" pattern of using a concurrent queue + barriers for a simple getter/setter of an ivar is very bad for performance, despite intuitively being right (especially asyncing the set).

~~Actually lemme walk that back slightly: If you aren’t using async, concurrent queues don’t really do anything, so only if you’re asyncing the set. But it still doesn’t make sense if you aren’t because a `sync` dispatch on a concurrent queue is a noop.~~

`queue.async(flags: .barrier)` will usually create a thread. Creating a thread costs multiple orders of magnitude more than setting an ivar usually does.

> If your setter (whatever you do in the write blocks of the queue) for some reason takes more than say, `1ms`, then it might be a good idea. Below that it’s not worth it.

## Lack of support for Priority Donation

Have you ever rode an express train, hoping that you'd get to your destination in half the time? Well imagine if this express train was stuck behind a local train that wants to stop at all stations. You have a bunch of options
- Get on foot and try out running the local train.
- Stay in your express train, while the local train stops at all stations. 
- Message the dispatcher and tell them "I'm stuck behind a local train. Can you bump its priority to express as well?" <-- This is Priority Donation!

There’s a problem in all lock-like systems where if a low priority thread is currently running, high priority threads will back up behind it and "effectively" run at low priority until it’s done. [Priority donation](https://web.eecs.umich.edu/~akamil/teaching/sp04/pri/) is a system for solving this by allowing the high priority thread that’s waiting to boost the low priority thread it’s waiting for, so it will get out of the way faster. 

Now you might wonder once a task is queued, then what does different does bumping up its prioirity make? Let's see what Apple says about that: 

> **About Quality of Service Classes**
>
> OS X implements a variety of resource management attributes, which can be adjusted in order to improve the responsiveness and efficiency of the system. For example, you can adjust the CPU scheduler and I/O priorities for a task, provide a threshold for timer coalescing, and denote whether the CPU should operate in a throughput- or efficiency-oriented mode. These attributes, however, can be difficult to access and configure. A much simpler solution is to utilize quality of service (QoS) levels—known as classes—in your app.
>
> A quality of service (QoS) class allows you to categorize work to be performed by NSOperation, NSOperationQueue, NSTask, NSThread, dispatch queues, and pthreads (POSIX threads). By assigning a QoS to work, you indicate its importance, and the system prioritizes it and schedules it accordingly. For example, the system performs work initiated by a user sooner than background work that can be deferred until a more optimal time. In some cases, system resources may be reallocated away from the lower priority work and given to the higher priority work.
>
> **Because higher priority work is performed more quickly and with more resources than lower priority work, it typically requires more energy than lower priority work**. Accurately specifying appropriate QoS classes for the work your app performs ensures that your app is responsive as well as energy efficient.

So when needed, the OS can give bump up the resources for a task and help it finish sooner. 

But the problem is that reader-writer locks/concurrent queues don’t support Priority Donation because supporting it would require keeping an arbitrarily large list of all the threads currently running so that they know which things to boost


This is definitely not a hard rule or anything; especially on newer devices there are situations where it can make sense to go wider than that. It’s about getting in a mindset where you’re only reaching for more threads in an intentional, considered way, rather than by default. Which is unfortunately not what was encouraged for years, and not what `libdispatch` or `NSOperationQueue` encourage in their design. 

This is something that was learned the hard way in the last few years. Like, remember your oldest target devices these days are typically what, iPhone 6? That’s a dual core device, so you’re not gonna have more than two threads actually running at the same time anyway. And your network calls are presumably using APIs that are themselves internally async

FWIW, this is somewhat less of a problem for apps than it is for libraries, because apps tend to have more control over their threads, whereas libraries expect to be called from anywhere

## Summary
- Try not using concurrency/asynchrony first. This really works amazingly well.
- If you need asynchrony, try to set it up as something like "main queue + async stuff serial queue" rather than using concurrent queues or many serial queues (e.g. "don’t go wide")
- If you need to protect some state, use `os_unfair_lock`. But the API of `os_unfair_lock` is awful in Swift, then just use a serial queue. 
- If you really need to "go wide", consider `DispatchQueue.concurrentPerform`
- If your task on the thread takes more that `1ms` then go for a concurrent + barrier approach. 

Good to watch:
https://developer.apple.com/videos/play/wwdc2017/706/

## Credits
Huge shout out to [David Smith](https://twitter.com/Catfish_Man). This post is all just me copying/piecing his answers to my questions.
