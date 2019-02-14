---
layout: post
tags: ["Swift"]
title: "Swift 3 Feature Highlight"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Our favorite open source language is changing at a rapid pace. Here, we'll check out some of my favorite additions that landed in Swift 3."
image: /assets/images/logo.png
---
Swift is celebrating milestone 3 already. It what seems like yesterday, Swift was just being announced to us a WWDC 14. Nothing was lost on the occasion, as Apple did a fantastic job introducing the world to what has become one of the most quickly adopted and loved languages around.

Swift's accelerated evolution means that the language seemingly only bears a passing resemblance now to what it did then. True to our nature as developers, it's time to embrace the change and get acquainted. What follows are a few interesting changes and enhancements coming with Swift 3 that caught my 👀s.

Shall we?

### The What: Addition by Subtraction

Decrement and incremental operators have a date with destiny. Soon, the days will be gone when using "- -" and "++" in front or behind variables compiles. Even now, you'll hit a compiler warning reminding you that Swift 3 is the harbinger of their imminent demise:
```swift
for var i = 0; i < 10; i++  
{  
    print(i)  
}
```
This will no longer work in Swift 3. In addition, [this isn't the preferred way to even get your loop on in Swift][1]. Instead, the astute Swift developer would've likely authored the previous code like this:
```swift
for i in 0 ..< 10  
{  
    print(i)  
}
```
### The Why

In short — these old operators are a carry over from C. And, lest we forget, Swift is "Objective-C without the C", so already — this doesn't fit. In fact, [the big man himself][2] saw this one through. They also present a barrier in learning Swift as they may not come naturally to first time leaners.

Moving forward, code that uses these are not ideal even if they are written with the truest intentions. Tack on the fact that they only apply to Integers and Floating Point Scalars — and you've got little reason to keep them here, especially since the best reason is "Well, they are already here."

It's good to see Swift trim the fat.

### The What: Nicer Debugging Identifiers

I mentioned this in [my last post][3], but hey — it's the little things, amirite? A little known trick used by the savvy Swift developer is to debug easier by using some of Swift's debugging identifiers.

* \__FILE__
* \__LINE__
* \__COLUMN__
* \__FUNCTION__
* \__DSO_HANDLE__

```swift
func myFunc()  
{  
    //Prints 'Executing myFunc()'  
    print("Executing " + __FUNCTION__)  
}
```
That syntax is just a bit loud and slightly unintuitive, earning its nickname "Screaming Snake Case". In Swift 3, those are refactored to work much like the new selector syntax:
```swift
func myFunc()  
{  
    //Prints 'Executing myFunc()'  
    print("Executing " + #function)  
}
```
### The Why

I think we can all agree that the new syntax is easier on the eyes by several orders of magnitude. As for the rest, I think the [original author][4] of the proposal says it best:

> "These are built into C's preprocessor and expanded before running the C-language parser. Swift's implementation differs from C's but offers similar functionality and, unfortunately, similar symbols. This proposal aims to break free of the historic chains of their unsightly screaming snake case, which look like boa constrictors [trying to digest][5] fully swallowed keywords."

### The What: G'Bye Objective-C String Constants Bridging

This one is great. It's just so, so easy to fat finger String values in programming. If you've spent a few minutes debugging a problem caused by a typo for a notification inside of NSNotificationCenter, you know this pain much too well.

This proposal can help with scenarios like that. Take a peek inside one of Apple's other open source titans, HealthKit:
```swift
HK_EXTERN NSString * const HKQuantityTypeIdentifierBodyMassIndex;  
HK_EXTERN NSString * const HKQuantityTypeIdentifierBodyFatPercentage;  
HK_EXTERN NSString * const HKQuantityTypeIdentifierHeight;  
HK_EXTERN NSString * const HKQuantityTypeIdentifierBodyMass;  
HK_EXTERN NSString * const HKQuantityTypeIdentifierLeanBodyMass;
```
In Objective-C — this is no harm done. We've all done it. Apple (obviously) does it. When the time comes to put these constants to use, it typically manifests itself as something like this:
```swift
+ (nullable HKQuantityType *)quantityTypeForIdentifier:(NSString *)identifier;
```
…in which case, it would be totally fine to pass the constant or just another String instance holding the same value.

### The Why

In either case, we're aren't getting compile time checks, which is always, always welcome. Moving forward, those constants are sucked into Swift like this:
```swift
public let HKQuantityTypeIdentifierBodyMassIndex: String  
public let HKQuantityTypeIdentifierBodyFatPercentage: String  
public let HKQuantityTypeIdentifierHeight: String  
public let HKQuantityTypeIdentifierBodyMass: String  
public let HKQuantityTypeIdentifierLeanBodyMass: String
```
It'd be better if those were instead represented as either an Enum or Struct, properly conforming to RawRepresentable. This change does exactly that. When it's all said and done, those values will look more like this:
```swift
enum HKQuantityTypeIdentifier : String  
{  
    case BodyMassIndex  
    case BodyFatPercentage  
    case Height  
    case BodyMass  
    case LeanBodyMass  
}
```
Which means one could invoke the previous function with a tad more intent and a lot more compile time checking:
```swift
let quantityType = HKQuantityType.quantityTypeForIdentifier(.BodyMassIndex)
```
👏

### The What: The Hardly Used Parlor Trick

I thought I would include one thing that I was actually sad to see go — but like a ill-fated relationship gone south, I get it. But it still hurts.

Tuple splatting is something likely none of you used daily, which is one reason it's going away. It entails the act of one passing a single tuple value (matching a function's parameters) to a function:
```swift
enum UserSex  
{  
    case Male  
    case Female  
}

func addUser(name:String, age:Int, sex:UserSex)  
{  
    //Add in user  
}

let newUser = ("Jordan", age:27, sex:UserSex.Male)  
addUser(newUser)

//Instead of addUser("Jordan", age:27, sex:UserSex.Male)
```
I thought this was a nice way to break up giant the staple, mile long list of parameters found all over iOS programming. It even felt Swifty in that it was something you might look at and think "Well, of course you can do that! Haha, crazy Swift! So neat!"

Alas, it wasn't meant to be.

### The Why

Hey, even Swift's author says that this behavior is "cute…and has some advantages" — but more importantly it has a few disadvantages that eventually gave it the axe.

The first knock? A call to addUser(tuple) really just looks like an overloaded version of addUser(name:String, age:Int, sex:UserSex). This is true for both the compiler and a maintainer, which means that it could potentially cause confusion.

More importantly, the current implementation isn't 100% reliable when it comes to deconstructing the tuple's arguments. In fact, it has quite a few bugs so each invocation using this method might as well be a game of Swift Russian Roulette.

### Bonus Points: Goodbye Bit

And this one just for laughs, the Bit type is out of here! See ya never!

And why?

The bit "was only used as the index for CollectionOfOne. We recommend using Int instead." Poor little fella, it was hanging on by a thread — a thread which was implicitly and quite literally only used in place (and an edge case, at that).

### Wrapping Up

Swift 3 is quite the achievement. I don't think just a few short years ago we would be standing in the trenches with Apple, plugging away at a release together. Let alone, on an entirely brand new language.

It's quite exciting, and once you get that Swift 2 codebase to play nice and juice up to 3, I think Swift will be that much more enjoyable to write in. For the entire collection of goodies that Swift 3 will bring, be sure to run over to Github for[ some light reading][6].

Until next time ✌️.

[1]: https://github.com/apple/swift-evolution/blob/master/proposals/0007-remove-c-style-for-loops.md
[2]: https://twitter.com/clattner_llvm?lang=en
[3]: {{ site.url | append:"/a-swift-refactor"}}
[4]: https://twitter.com/ericasadun
[5]: https://s-media-cache-ak0.pinimg.com/originals/59/ea/ee/59eaee788c31463b70e6e3d4fca5508f.jpg
[6]: https://github.com/apple/swift-evolution#implemented-proposals-for-swift-3
[7]: https://twitter.com/jordanmorgan10
