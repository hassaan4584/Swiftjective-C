---
layout: post
tags: ["Foundation"]
title: "Quality of Service"
author: Jordan Morgan
description: "Threading, concurrency and parallelism can be a sticky subject. Thankfully, Foundation has an API to make everything safer."
image: /assets/images/logo.png
---
All things are not created equal.

We've heard this phrase used in numerous contexts, but when it comes to Apple's asynchronous execution API — it's the law of the land. Grand Central Dispatch has always been fast, helpful, and necessary — but it's also a little magical at times. Reading documentation over things happening at the Unix level of the system can be a bit daunting.

But with iOS 9 and the all mighty multitasking API lurking around the corner, it's never been more important as a seasoned iOS developer to know how to properly wield the power of GCD.

This week, we'll uncover one of its most welcome features — the quality of service API.

### It's About Priorities

Apple itself has decreed to the development community at large that it's indeed our obligation to program in an energy efficient manner. While Cupertino sure has done their part of the work (tight integration of hardware and software, intelligent application state management, etc.) the onus falls on us to solve other important facets of the problem.

The quality of service API, or QoS for those developers who'd love yet another acronym, was actually introduced in iOS 8. It arrived to not much discussion by the community, but it should be given as much pomp and flair as the other forward facing technologies. With it, one can assign priority levels to the work an app carries out.

QoS can be applied all over iOS as well. One can prioritize queues, thread objects, dispatches queues, and POSIX threads. This is important, since asynchronous work is typically spread out across all of these techniques. By assigning the correct priority for the work these methods perform, iOS apps remain quick, snappy, and responsive.

In short, using QoS appropriately will not only help applications be more energy efficient, but….more….efficient efficient. Make sense? Good.

### Levels of Priority

After an app kicks off and starts a runloop on the main thread, one can begin taking advantage of QoS. QoS breaks out priorities into four different groups. Each one corresponds to common tasks one might find themselves coding in their iOS endeavors.

* **User Interactive**: Work that happens on the main thread, such as animations or drawing operations.
* **User Initiated**: Work that the user kicks off and should yield immediate results. This work must be completed for the user to continue.
* **Utility**: Work that may take a bit and doesn't need to finish right away. Analogous to progress bars and importing data.
* **Background**: This work isn't visible to the user. Backups, syncs, indexing, etc.

The idea is simple. Assign a priority to your work. In psuedo code, it looks like this:
```swift
block = dispatch_block_create_with_qos_class(0, QOS_CLASS_UTILITY, 0, ^{//work})  
dispatch_async(queue,block)
```
### Automatic Propagation

Upon inspection, one might notice that QoS as a whole seems to form a hierarchical structure — and one would be correct. This helps with a process known as automatic propagation.

When competing threads hand out fisticuffs, GCD steps in as the responsible bouncer and puts an end to the bickering. It does so, in part, by looking at each one's QoS to determine their intent and classification of work. In this case, QoS is acting like an abstract parameter to tune not only its owner's operation, but others ones as well.

While we are chatting about the topic — it's worth nothing that there are actually two more classes that live in QoS: Default and Unspecified. As developers, we shouldn't need to interact with these directly.

Default falls between user initiated and utility. Work that has been bastardized by the developer (you monster) and hasn't been assigned QoS gets this one. That's not actually a bad thing, as it makes sense a lot of the time….except when it doesn't. So pay attention.

Unspecified represents the complete absence of QoS information and tells the system that an environmental QoS should be inferred. This will happen by no fault of your own, as threads can be unspecified if they are utilizing legacy APIs which can opt of QoS entirely.

No matter the selection, QoS shouldn't be thought of as a static assignment. It's rather a living entity that can certainly bend over time depending on what work is being executed, when it's being executed, and where it's being executed. If, for example, the QoS of a queue doesn't match with the QoS of an operation, then QoS might be inferred.

When push comes to shove though, one can assign priority to your priority (whoa) by using DISPATCH_BLOCK_ENFORCE_QOS_CLASS which will ensure the block's QoS is used, and not the queues. Useful, for say, a log out process.

Regardless, GCD has to know priority. Either you tell it that priority, or it picks for you. It's smart at taking a stab at the pick, but it's essential that you be intentional with QoS so that GCD can power up and kick things up to 10.

### Speaking of Picking…

Whether it's a operation queue, block, or queue one is prioritizing, the API to do so is very succinct. For instance, on an operation:
```swift
let q:NSOperation = NSOperation()  
q.qualityOfService = .UserInteractive
```
Whereas with a block:
```swift
let priority = dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0)  
dispatch_async(priority, {   
    //Open up a document  
})
```
And that's it. Assigning to queues and pthreads looks similar as well. Thankfully, with only a few lines of code the user experience can be tremendously benefitted and one is also being a good platform citizen.

Optimizing further with GCD's API is where things really get fun. If one is creating some work that has nothing to do with the flow of execution, for instance, the DISPATCH_BLOCK_DETACHED flag can be called to action:
```swift
dispatch_block_create(DISPATCH_BLOCK_DETACHED, {  
    //Work  
})
```
The benefit here is that GCD knows the work being done inside the block has nothing to do with the flow of execution. This cuts out some of the fluff, like assigning an activity ID, QoS propagation, and other properties of the execution context.

### So Many Questions

To enhance your QoS journey, I've found it helpful to ask a series of questions. Though not all the possible grounds are exhausted, they generally cover most practices. Those questions look something like this:

* Am I doing work that actively involves updating the UI thread, such as scrolling or loading a photo? **User Interactive**.
* Am I doing work that requires user interaction? Can no meaningful progress be made until it's completed? **User initiated**.
* Am I doing work that the user isn't aware of, like downloading some background content? **Utility**.
* Am I not doing any of those? Am I cleaning up a database or doing some maintenance? **Background**.

### Wrapping Up

With the right QoS assigned, GCD can be better at so, so much. CPU scheduling priorities, timer coalescing, I/O priority, CPU throughput versus efficiency, and more. QoS is pertinent to understand because of these things, but thankfully the API is easy to use.

So go ahead, play favorites with your asynchronous work — tell GCD which ones are important and which ones can wait. 

Until next time ✌️.