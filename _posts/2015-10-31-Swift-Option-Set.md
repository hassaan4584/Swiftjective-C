---
layout: post
tags: ["Swift"]
title: "Option Set"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Option Sets solve a large swath of problems, but you may not think to use them at first. Looking at how they work and why their useful might change your mind."
image: /assets/images/logo.png
---
Steve Jobs built an entire empire by selling us amazing products that we didn't even know we wanted. Those little epiphanies that he must've had during his short time on this earth had to be utterly satisfying to share.

Likewise, Swift 2 is loaded with delightful little treats that fit the "Uh, I didn't know I wanted this but now I love this" mold perfectly. This week, we look at such a concept — Swift's option sets.

### Option What?

At the end of the day, you are what you are. I've heard option sets described several different ways, but I've found it's best to define the terms cut and dry.

And if one describes option sets without all the fluff — all they are is a set of boolean values. As in a literal set collection. [Hey — we've talked about those before, right?][1]

### But Why?

Before option sets were unleashed to longing Swift developers, we represented the concept a few different ways. The problem, however, is that most of them were archaic and slightly error prone.

To combine multiple flags together, one would employ a C like syntax. And, lest we forget — Swift is Objective-C without the C. In addition, to identify the flags one desired, one would usually just _or_ them together:

```swift   
[UIView animateWithDuration:0.5f delay:0 options:UIViewAnimationOptionAllowUserInteraction | UIViewAnimationCurveEaseIn animations:^{  
    [self.view layoutIfNeeded];  
}completion:nil];
```
You may have no qualms with this whatsoever, and I would lovingly hear you out. But problems arise down the development road. For instance, one could represent option sets with a nil value:

```swift 
[UIView animateWithDuration:0.5f delay:0 options:nil animations:^{  
    [self.view layoutIfNeeded];  
}completion:nil];
```

This is bad news for Swift, because optionals and option sets aren't at all the same things. To present the case further, most developers extract these values using bitwise operations which can lead to error prone code quite quickly.

### Safety First

To take these challenges on, Swift 2 houses option sets tidily in a set collection. Why is that great?

* You can treat empty options sets as [ ] (no more 0 or nil)
* Access to full Set API
* Hyper efficient
* Trivial to define custom option sets

### Make It Modern

You've likely been enjoying option sets in Swift without even knowing it. Swift actually goes to the trouble to simplifying Objective-C's bitflags in the form of NS_OPTIONS into sets conforming to OptionSetType. For instance, take this old relic of the past (because you are assuredly using auto layout, right?):
```swift   
typedef NS_OPTIONS(NSUInteger, UIViewAutoresizing)  
{  
    UIViewAutoresizingNone = 0,  
    UIViewAutoresizingFlexibleLeftMargin = 1 << 0,  
    UIViewAutoresizingFlexibleWidth = 1 << 1,  
    UIViewAutoresizingFlexibleRightMargin = 1 << 2,  
    UIViewAutoresizingFlexibleTopMargin = 1 << 3,  
    UIViewAutoresizingFlexibleHeight = 1 << 4,  
    UIViewAutoresizingFlexibleBottomMargin = 1 << 5  
};
```
Testing for these types of bitflags required lower level bitwise math. You've likely seen code evaluate conditions using such methods, but that was then and this is now. And in the here and the now, we use the set API. Queue trivialized code:
```swift  
if someResizeOptions.exclusiveOr(moreResizeOptions).count > 0  
{  
    println("Each view has a unique resizing mask")  
}
```
### What I Have Created

Defining an option set is a straight forward task, and it's also one that's made much simpler thanks to the magic of [protocol oriented programming][2].
```swift   
struct DIBApps : OptionSetType  
{  
    let rawValue: Int  
    init(rawValue: Int) { self.rawValue = rawValue }

    static let SpendStack = DIBApps(rawValue: 1)  
    static let HaloTimer = DIBApps(rawValue: 2)  
    static let NewTVOSApp = DIBApps(rawValue: 3)  
    static let AllApps:DIBApps = [SpendStack, HaloTimer, NewTVOSApp]  
}
```
The small price of admission is essentially conforming to OptionSetType. OptionSetType is nice too because it segues conformance to SetAlgebraType for any type whose RawValue is a BitwiseOperationsType.

The previous code is simply safer, easier, and much more readable than how things were done before. Now instead of using bitwise operations, oring things together, passing nil where it really doesn't make sense — option sets just look and feel like first class citizens (because they are).

So — no more of this:
```swift 
someFunction(apps:nil)
```
or that:
```swift  
someFunction(apps:0)
```
But instead, this:
```swift  
someFunction(apps:[])
```
And, to put it in more relatable context:
```swift 
let animationOptions:UIViewAnimationOptions = [.CurveEaseOut, .Repeat]
UIView.animateWithDuration(0.5, delay: 0, options: animationOptions, animations: { void in /*animation*/ }, completion: nil)
```
Ahhh, type safety.

### Wrapping Up

I've often shown a healthy magnitude of love towards the more mundane parts of Swift in my writing. Though you may only use such concepts once in a blue moon, it's immensely satisfying to know that one has employed the right tool for the job.

And — that's exactly what we get with option sets. A first class set of booleans, with unfettered access to the collection's API, no compiler matching requirements, and perhaps most exiciting — no C like syntax.

Until next time ✌️.

[1]: {{ site.url | append:"/swift-set" }}
[2]: {{ site.url | append:"/protocol-oriented-programming" }}
