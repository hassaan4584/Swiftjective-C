---
layout: post
tags: ["Swift"]
title: "#keyPath"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Selectors are a part of iOS development daily. They can also be a common source of frustrating bugs, but #keyPath is here to help."
image: /assets/images/logo.png
---
I love video games. That may not come as a surprise, given that I am a programmer by trade. And for years I've been plagued by one tiny problem in the domain. The current Xbox One's power button is so easy to accidentally tap and cut your precious gaming session short. It's flat, hidden and not very practical. So, during E3 2016 — when Microsoft proudly displayed the next Xbox, it was telling that I was most taken with one tiny detail.

A _tactile_ power button. A small, incremental, and applicable improvement that just makes thing so much better.

So today — we'll talk about the Swift 3 equivalent of that — #keyPath().

### The Typo — No Longer

Let's agree on two simple truths in iOS programming. One will inevitably make a typo, and one will also use K.V.O somewhere down the beaten path. The former is true regardless of your programming language of choice.

But, since the API is driven (mostly) off of a fragile string literal — things go wrong far too easily:
```swift 
let aController = UIViewController()
aController.addObserver(self, forKeyPath: "OH_GEEZ_HOPE_DIS_RIGHT", options:[], context: nil)
```
True, some of this is mitigated by global variables, constants and enumerations. It's a shame, though — since Foundation, UIKit and everything else in between makes clever use of the tech. This is like giving your freshly minted 16 year old son a Bugatti Chiron for his first vehicle.

And, being able to observe changes for any key-path on an ad-hoc basis can yield flexibility and promote abstraction, but the truth remains that it's far too easy to scrub. Swift 3 smashes this problem to absolute oblivion by enforcing compile time checks on such key-paths.
```swift 
let aController = UIViewController()
aController.addObserver(self, forKeyPath: #keyPath("anObject.someProperty"), options:[], context: nil)
```
### In Practice

Using #keyPath(), a static type check will be performed by virtue of the key-path literal string being used as a StaticString or StringLiteralConvertible. At this point, it's then checked to ensure that it A) is actually a thing that exists and B) is properly exposed to Objective-C.

So, with our first code sample, clang would kindly tell us that "OH_GEEZ_HOPE_DIS_RIGHT" doesn't exist in the current context. Before, this was assuredly Objective-C Roulette, as the alternative was finding out the key-path was bogus by the way of a runtime exception. No longer:
```swift 
let aController = UIViewController()

//This line produces a compile time warning since it's now  
//A compiler checked expression  
aController.addObserver(self, forKeyPath: #keyPath("OH_GEEZ_HOPE_DIS_RIGHT"), options:[], context: nil)
```
Level with me and think about just how easy it is to get this wrong:
```swift 
class Foo  
{  
    let aProp:String = "A Value"  
}

let anObject = NSObject()

anObject.addObserver(anObject, forKeyPath: "Foo.aPop", options: [], context: nil)
```
Unless one reads with extra intent, it's easy for the eyes to scan right past the error in the previous code, "Foo.aPop", which is not a valid key-path. Using the #keyPath() expression, we wouldn't even compile — which is exactly what we'd want to happen.

By ensuring matches to valid key-paths — even the common refactor is no longer a dangerous proposition:
```swift 
class Foo  
{  
    let aRenamedProp:String = "A Value"  
}

let anObject = NSObject()

//Compile time error is produced since #keyPath() contains the   
//Old property name  
anObject.addObserver(anObject, forKeyPath: #keyPath("Foo.aProp"), options: [], context: nil)
```
Better yet — #keyPath() packs in autocompletion as well, so it's even less likely for one to author such code to begin with. Bravo.

I know this piece is lighter on content than most entries, but that's telling in of itself. I love this feature, and it's elementary to explain — a hallmark of beautiful refactoring in the world or programming. There really isn't much to say, other than it's great and certainly a delightful enhancement.

Be sure to say hi to #keyPath()'s close cousin while you're in town too, #selector().

### Wrapping Up

Listen, I think #keyPath() is my spirit animal.

I've spent an embarrassing amount of time chasing down a bug that was produced by a misspelled string. KVO and KVC were always great tools for any iOS developer to wield, but now one can utilize such techniques with a greater peace of mind that only clang can bring.

To me, this Swift 3 tweak is just begging to yield war stories to the next generation of Swift developers. I can see us all now, "Back in my day, you had to just type a key-path and hope it was right! None of this sticky, compile time checking. That's right, we were so _alive_ back then! And — we didn't even have ABI compatibility!"


Until next time ✌️.