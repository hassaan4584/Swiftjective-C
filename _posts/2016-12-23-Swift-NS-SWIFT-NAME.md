---
layout: post
tags: ["Swift"]
title: "NS_SWIFT_NAME"
author: Jordan Morgan
description: "Interop is required in the world we live in. Here's an attribute in Swift that can help make things easier."
image: /assets/images/logo.png
---
In the spirit of the quickly approaching holidays, let it be said that I am full of joy for both Swift _and_ Objective-C. And, well — so are a ton of other seasoned developers. Apparently, [only some 11% of the top 100 downloaded apps][1] contain any Swift at all .

So of course — interop between the two languages is key if we're to push this thing forward as a community, [and it's something we've talked about before][2]. The Swift compiler's quite verbose implementation typically makes bridging between the two languages elegant and manageable.

Alas, it doesn't always do what you want, especially in terms of how Objective-C code gets named. And if you want to do something about that, then you'll love the NS_SWIFT_NAME macro, this week's topic.

### The Why

Swift has some hard and fast guidelines on how methods should be authored, enumerations are presented and how option sets function. 💯!

And, they almost all differ from Objective-C. 🙃.

So when one uses both Swift and Objective-C together, it's well known that the bridging header helps make the connection between the two code bases while working to reconcile their differences. Objective-C class factory methods become a first class Swift initializer, and those insanelyLongEnumerationCaseNames becomes truncated names. Etcetera.

But things may not always be converted how you might expect, so the macro in question can help rectify code where the conversion went south.

### The How

It's super simple — like much of Swift. And that's a great and beautiful thing. Consider this factory method:
```swift
+ (instancetype)mileageWithMPG:(NSUInteger)MPG;
```
Standard fare for Objective-C code, as we'd expect an instance of this class to be returned with its miles per gallon (i.e. MPG) property assigned to. In Swift, however, this may be picked up as something else — say a function. Using NS_SWIFT_NAME, we can make things right:
```swift
+ (instancetype)mileageWithMPG:(NSUInteger)MPG NS_SWIFT_NAME(init(MPG:));
```
Now, when the Swift compiler does its magical dance and the Swift gnomes come out of your MacBook to expose both Swift and Objective-C in angelic harmony — this particular piece of code translates over to Swift as such:
```swift
let mileageRecord = Mileage(mpg: 30)
```
The macro just takes in one argument, which is the initializer you're after in your Swift codebase in this example. As you'll see, the argument can allow for some other spiffy things, though.

### On the Flip Side

The same can be said of instance functions. The Swift Compiler could very well pick those up as initializers, especially if the function was authored to return id:
```swift
+ (id)mileageWithVariance:(double)variance;
```
In Swift — it might be exposed as something like this:
```swift
let mileageRecord? = Mileage(variance: 30)
```
Which, of course — we don't want. Moreover, it's just flat out wrong. Fixing it using the macro is just as simple. In fact, it's only a matter of switching out the argument with a function instead of a initializer as we did previously:
```swift
+ (id)mileageWithVariance:(double)variance NS_SWIFT_NAME(mileage(variance:));
```
And then all is well within the land.

### Enums

Given that Swift truncates enumeration value name prefixes, one can also customize an individual value using NS_SWIFT_NAME as well. Consider the following:
```swift
typedef NS_ENUM(NSInteger, DIBCoinFlip) {  
    DIBCoinFlipHeads,  
    DIBCoinFlipTails NS_SWIFT_NAME(YupItsTails),  
};
```
Certainly a contrived example — but it's a cool tool to have around none the less, should you need it.

### Wrapping Up

Reflecting on how Swift has undoubtedly changed iOS development — I've found one of the things I love most is discovering little nuggets like the NS_SWIFT_NAME macro that enhance the use of both languages.

It shows that Apple is pushing the new while respecting the old. And, well, the old is going to be around for a long time still — so that's good news.

Until next time ✌️.

[1]: https://medium.com/@ryanolsonk/are-the-top-apps-using-swift-42e880e7727f#.af595pf7u
[2]: {{ site.url | append:"/swift-objective-c"}}
