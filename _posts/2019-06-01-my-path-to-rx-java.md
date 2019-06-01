---
layout: post
title:  "My Path to Learning RxJava"
date:   2019-06-01 14:00:30 -0500
categories: android,rxjava
---
# The Story
A little while ago, I decided to make a (rather) scary decision: I'm going to learn RxJava. I don't mean to be negative or anything here: you should always push yourself to learn something new. The bigger the challenge, then most often the bigger the reward. However, there are several  reasons as to why I was a hesitant to dive into RxJava at first:

1. It's huge. RxJava has grown into being a very sophisticated and powerful library. According to Chris Banes [from a talk he gave](https://www.youtube.com/watch?v=P7ov_r1JZ1g) (which was actually about Kotlin coroutines), there are nearly 300 operators in RxJava. In addition, you have the subtle differences between RxJava 1 and 2 and a **_steep_** learning curve to get over.
2. This one goes hand in hand with my previous point: things get complicated pretty quick. Let me elaborate on this. Let's suppose you're just learning RxJava for the first time. You get excited because you finally understand observables, observers, and the subscription. You write your first lines of RxJava with something like `Observable.just()` or `Observable.create()` and everything works great. However, once you add a couple operators or start working with multiple subscriptions, things might not work so well anymore. Things get complicated quickly and it goes hand-in-hand with that steep learning curve. 
3. It's difficult to debug once you run into a problem. I haven't yet had to debug RxJava _too_ much so far and the only way I've done so is by adding `doOnNext()` and logging what's happening. That alone doesn't always point out what the problem is though. Other developers have also ran into debugging troubles and I haven't heard anyone be overly optimistic.
4. Kotlin co-routines have been getting a lot of momentum. The overall consensus, from what I've been able to gather, is that they're easier to use to handle a lot of the same scenarios that RxJava would be used in.

With all of that being said, I'm sure you must be wondering "What's the point then?". I can come up with a few reasons to push yourself to learn RxJava:

1. The power is _there_. If you have some crazy multithreading scenario that you've ran into with your codebase, it's very likely you'll feel compelled to use RxJava.
2. I've personally just always wanted to learn it. From all of the reading that I've done about it, I simply find it extremely interesting. And that's largely due to my general interests in threading, concurrency, and parallelism.
3. As software developers, we're going to have to adapt to a reactive world. I think it's easy to say that software needs to be as real-time and on-demand as possible in today's world. And those things can only be accomplished with libraries such as RxJava. The more tools under our belt, the better.
4. I'll be prepared if/when I run into it in some other codebase, whether with side projects or in the work place. It can't be denied the widespread adaptation of RxJava, so it's likely that I'll see it _somewhere_.

With all of this being said, I figured I'd share some resources with everyone. There are an immense amount of resources available to learn RxJava and a lot of people will point you to different books, videos, presentations, etc. However, these are the resources that have helped me the most in my path of learning to be reactive:

## Fragmented Podcast Episodes 3 & 4
If you aren't already listening to [Fragmented](https://fragmentedpodcast.com/), I highly recommend it. The hosts, Donn Felker and Kaushik Gopal, have been instrumental in my progressive passion for Android development, especially in my early stages. It's also a really convenient way to learn about Android dev since you can learn something while you're driving, sitting on the train, etc. [This episode](https://fragmentedpodcast.com/episodes/3/) is where I first started to understand the fundamentals of RxJava: observables, observers, and the subscription. Dan Lew is also a great resource with his [blog's tutorials](https://blog.danlew.net/2014/09/15/grokking-rxjava-part-1/) on the subject.

## Kaushik's Talk on RxJava
Kaushik, one of the hosts from Fragmented, gives this [great talk](https://www.youtube.com/watch?v=k3D0cWyNno4&t=3218s) about RxJava. He also presents some examples and use cases of Rx. Even though the talk is several years old by this point, it's still relevant. And you can find the source code to all of his examples [here on GitHub](https://github.com/kaushikgopal/RxJava-Android-Samples).

## Reactive Programming with RxJava by Tomasz Nurkiewicz and Ben Christensen
[This book](https://www.amazon.com/Reactive-Programming-RxJava-Asynchronous-Applications/dp/1491931655/ref=sr_1_fkmrnull_1?crid=JDT6AK84KQQU&keywords=rxjava+oreilly&qid=1553962255&s=gateway&sprefix=oreilly+rxjava%2Caps%2C152&sr=8-1-fkmrnull) is great. It was recommended to me by one of my co-workers and has been a great resource in learning Rx. This book does a great job of conceptually understanding RxJava: how to master `Observable.create()`, handling multiple subscriptions without creating memory leaks, learning about some of the most commonly used operators and differentiating some of them (what's the difference between `map()`/`flatMap()` or `merge()`/`concat()`), and so much more. This is a book which I highly recommend.

In terms of which resource to start with, I'd actually recommend going through them in the order they're listed. The podcast was the best start for me, followed by the presentation. The book itself is good, but I think it'd be helpful if you listen/watch at least one of the other resources first.

And that's about all that I have for you, folks. Next step: actually _using_ RxJava in a side project! I plan to be doing just that. I've created a project where I react to broadcasts sent by the Android framework. Stay tuned for a future post about just that!
