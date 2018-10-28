---
layout: post
tags: ["Foundation"]
title: "Copy on Write"
author: Jordan Morgan
description: "Yet again, Foundation dishes out free optimizations without us having to lift a finger. Let's see how collections became a bit smarter."
image: /assets/images/logo.png
---
Foundation is the unsung hero of iOS development. Powerful due in part to its maturity and ubiquity — it boasts all of six major implementations, it treats engineers to everything from the basic object to annual optimizations under the hood at WWDC. And the best optimizations are the ones I don't even have to make. They just happen.

With copy on write semantics for Foundation collections, this week's topic, that's exactly what we get.

### C.o.W Primer

Both the amatuear and the veteran programmer know that we don't go far without embracing collections, yet by and large there isn't much thought or questioning that occurs as to what their doing behind the scenes or how exactly they work beyond our CompSci 101 course.

It can sometimes serve as intimidating subject matter, but luckily copy on write is both a simple concept and, as such, is easy to conceptualize. The elevator pitch is that instances pointing to the same object shouldn't need to employ full copies unless one of them, does in fact, mutate:
```swift
NSMutableArray *ar1 = [@[@"TTIDG",@"Pizza"] mutableCopy];  
NSMutableArray *ar2 = [ar1 mutableCopy];
```
Here, both variables hold the same data. So why create a full copy unless it's actually needed? This means that instead of creating the full copy, we can just utilize pointers to the same backing store and enjoy a much more efficient O(1) operation.

All we're talking about here is an optimization strategy. Even better, the whole technique is all in the name. We shouldn't copy data unless it's written to, in which case — we have a reason to carry forward the actual copy. Copy on write.

So, TL;DR — copies are super cheap now. In fact, they are almost downright free for defensive copying code. In the past, they were typically linear at best.

### Foundation Implications

This means that our good friends NSSet, NSDictionary and NSArray will all benefit from the optimization made by the folks maintaining the Foundation Framework. Going back to our previous example:
```swift 
NSArray *ar1 = @[@"TTIDG",@"Pizza"];  
NSMutableArray *ar2 = [ar1 mutableCopy];

// Currently, both arrays point to the same backing store

// Now a mutation occurred, with CoW - this is where the copy actually takes place  
[ar1 removeLastItem];
```
A large number of application flows might not ever reach a mutated state, thus the copy operation was effectively wasted effort. If it doesn't occur, the only hit we'll take with CoW is the allocation of the second collection — which likely won't be much.

But — be mindful of what it isn't:
```swift
NSArray *ar1 = @[@"TTIDG",@"Pizza"];  
NSMutableArray *ar2 = ar1;
```
This isn't the same at all, and copy on write doesn't apply. Here we're pointing to the exact same reference in memory. Remember that this particular optimization is all about efficient copies. Even though pointing to a reference and copying data deals with sharing said data, they are much different in implentation.

### Applications

Even though copy on write is a performance optimization, it shouldn't soley be thought as one in pragmatic terms. It's great that it's there, we'll benefit from it without having lifted a finger — but how we can leverage it to do things we maybe couldn't before?

[Session 244 at WWDC 17][1] offered a few great examples. Suppose we have an array property:
```swift 
@property (strong) NSArray *foo;
```
The intention of the declaration implies that it shouldn't have mutable state. But hey — life comes at you fast:
```swift   
// Trolling  
self.foo = [NSMutableArray new];
```
Now, consider this:
```swift  
@property (copy) NSArray *copiedArray;  
@property (strong) NSArray *stongArray;

self.copiedArray = [NSMutableArray new];  
self.strongArray = [NSMutableArray new];

// Logs out 'No'  
NSLog(@"%@", [self.copiedArray isKindOfClass:[NSMutableArray class]] ? @"Yes" : @"No");

// Logs out 'Yes'  
NSLog(@"%@", [self.strongArray isKindOfClass:[NSMutableArray class]] ? @"Yes" : @"No");
```
The copy attribute has been around since the beginning — and it's commonly put to great use with class clusters. What's happening here is that sending a copy message to something that is mutable (i.e. the NSMutableArray here) will still return an immutable copy. Copy on write makes life even faster in such a scenario.

Continuing on with Objective-Cisms — mutable collections are often used to build something up. This is a common technique found within many functions, where the author will then declare the return type as immutable. Yet, since a mutable array is a kind of array, this is perfectly legal:
```swift  
- (NSArray  *)allEmployees {  
    NSMutableArray *container = [NSMutableArray new];

    //build up container with employees...  
    //i.e. [container addObject:anEmployee]

    return container;  
}
```
And innocently enough, we've sharing mutable state again. The method header deceives any caller now, the 🍰 is a lie.

Since we've got copy on write, we don't even need to bother with such a qundary since copies are cheap. We can do the right thing and sleep easy on performance hits:
```swift
- (NSArray  *)allEmployees {  
    NSMutableArray *container = [NSMutableArray new];

    //build up container with employees...  
    //i.e. [container addObject:anEmployee]

    return [container copy];  
}
```
Also — Swift 👋?

Think about the bridging process. If you, or perhaps another framework, have a foundation collection returned to you that one has to use in Swift code — you're getting the value type of the collection since the language employs value type semantics.

How does one enforce that 🤔?

…with a copy on the reference type during the bridging process. So now if that bridged data was never mutated, the whole bridging operation's efficiency is greatly improved. Which, I suppose, is a nice seque to wrap things up.

### Final Thoughts

As I positied in one of [my more popular articles a few years ago][2], Objective-C has become stronger and more capable due to Swift's presence alone. The sea of apps using Objective-C runs deep, and now their collections run faster. It's not surprising, as copy on write semantics have been with Swift developers since the beginning.

And now, here we are, with Objective-C getting in on the fun now. #DinosaurNotDead, am I right?

Until next time ✌️.

[1]: https://developer.apple.com/videos/play/wwdc2017/244/
[2]: {{ site.url | append:"/objective-c-in-2015"}}