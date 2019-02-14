---
layout: post
tags: ["Swift"]
title: "Pattern Matching"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Pattern matching with Swift yields concise code when it otherwise might be a bit more convoluted. Here's how it works."
image: /assets/images/logo.png
---
The life of a developer is an assuredly busy one. Today's API is tomorrow's dated framework. Objective-C to Swift. Change is the only constant.

That sort of thing.

Given this realization, it becomes pertinent to familiarize one's self with some consistencies in programming, such as language conventions and features. Though not safe from the constant winds of change, they at least stay put for a while.

This week, we'll look at such a feature in Swift: pattern matching with tuples.

### The Exposition

The friendly key-value data structure known as the [tuple][1] can play all sorts of roles for the adroit developer. No matter, one may not have thought to apply them to Swift's pattern matching.

But they can be:
```swift
let dibApp = (1.0, "Spend Stack")

func releaseDate(version:Double, appName:String)  
{  
    switch(version, appName)  
    {  
        case (1, "Spend Stack"):  
        print("August 14th, 2014")  
        case (1.1, "Spend Stack"):  
        print("October 8th, 2014")  
        case (_, _):  
        print("Who knows")  
    }  
}

//August 14th, 2014  
releaseDate(dibApp.version, appName: dibApp.app)
```
Swift's suite of pattern matching capabilities always struck me as both useful and practical. It takes something simple and empowers it be as complex as the situation calls for.

For instance, take the tuple itself.

It's nothing more than a comma separated list of values. Inherently, it demonstrates pattern matching quite frequently. The pattern (1, 2) matches perfectly fine with (foo, bar) by virtue of their like structures.

This truth allows the previous switch statement to both compile and properly execute. But it can be more diverse, should one desire:
```swift
let dibApp = (version:1.2, app:"Spend Stack")

func releaseDate(version:Double, appName:String)  
{  
    switch(version, appName)  
    {  
        case (1, "Spend Stack"):  
        print("August 14th, 2014")  
        case (1.1…1.3, "Spend Stack"):  
        print("Between October 2014 and February 2015")  
        case (_, _):  
        print("Who knows")  
    }  
}

//Between October 2014 and February 2015.  
releaseDate(dibApp.version, appName: dibApp.app)
```
### The Climax

But still, one yearns for more. And that's okay, because Swift has obliged via pattern matching against associated types. Due to its healthy relationship with enumerations, sifting out the perfect value is accomplished in a syntactically pleasing fashion:
```swift
enum Platform  
{  
    case Uknown  
    case iPhone(latestSupportedModel: Int)  
    case iPad(latestSupportedModel: Int)  
    case watchOS(latestSupportedModel: Int)  
    case Universal  
}

let dibApp = (version:1.1, app:"Spend Stack", target: Platform.iPhone(latestSupportedModel: 5))

func releaseDate(version:Double, appName:String, target:Platform)  
{  
    switch(version, appName, target)  
    {  
        case (1, "Spend Stack", Platform.iPhone(latestSupportedModel: 1)):  
        print("August 14th, 2014")  
        case (1.1…1.3, "Spend Stack", Platform.iPhone(let latestSupportedModel)) where 5…6 ~= latestSupportedModel:  
        print("Multiple release dates: UI tweaks for taller devices")  
        case (1.0, "Halo Timer", _):  
        print("June 20th, 2015 on  watch, iPhone, and iPad)"  
        case (_, _, Platform.Universal):  
        print("All universal release dates")  
    }  
}

releaseDate(dibApp.version, appName: dibApp.app, target: dibApp.target)
```
Of special note is the second switch statement. Here, Swift is kind enough to bind the enumeration's associated type (an integer) at run time when the switch is executed. Specifically, that's taking place here:
```swift
...Platform.iPhone(let latestSupportedModel))...
```
Once the value is properly bound, a guard statement then knows to carry out its pattern matching — which in this case checks for all iPhone Models numbered 5 through 7. This statement only executes when the extracted value of _latestSupportedModel_ is properly matched in the given range:
```swift
case (1.1…1.3, "Spend Stack", Platform.iPhone(let latestSupportedModel)) where 5…6 ~= latestSupportedModel:
```
Moreover, wildcard matches can be combined with the previous method of pattern matching. In fact, they are one and the same in theory:
```swift
case (_, _, Platform.Universal):
```
By destructing values into a temporary variable, constant, or optional binding, Swift allows one to be incredibly intentional with their matches. One could also constrain matches to values of only a certain type through the use of data annotations.

At the end of the day, there are two basic approaches of pattern matching in Swift:

* Successfully matching any kind of value
* Matching that may fail to match a specified value at runtime

### Wrapping Up

Tuples, the power of pairs. Pattern matching, the power of paring down. It's certainly a combination that provides a graceful way to sort through the cruft to extract what one needs. Though pattern matching is a technique well matured in its own right, I personally find it to be an art worth exploring due to its wide range of useful…helpful (applications, use cases).

Until next time ✌️.

[1]: {{ site.url | append:"/swift-tuples" }}