---
layout: post
tags: ["Trivia"]
title: "WWDC 2019: The Pregame Quiz"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "iOS 13 is coming. Before Marzipan and Dark Mode show their respective faces, let's enjoy another annual pregame quiz!"
image: /assets/images/logo.png
---
Dark mode, Marzipan and who knows what else await us which means we're not far from Tim Cook and Friends revealing iOS 13 to the world. With a ton of services announcements already taken care of such as Apple TV+, Apple News+ and more - the tea leaves indicate that they're going hard on pure API news this year.

At this point it's all conjecture, so let's ready up with the fifth annual Swiftjective-C WWDC Pregame Quiz!

If you'd like a quick primer on how this all works or how it got started, check out the first four quizzes from [2015][1] ,[2016][2], [2017][3] and [2018][4].

Now - Lets.Play(with:🔥)!

### Ground Rules

There are three rounds, and the point break down is as follows:

* **Round 1** – 1 point each answer
* **Round 2** - 2 points each answer
* **Round 3** - 3 points each answer

The last question of each round is an optional wildcard question. Get it right, and your team gets **4** **points**, _but_ miss it and the team will be **deducted 2 points**.

### Round 1 — Swift Decisions

**Question 1:**  
What's the name of the Swift attribute, added in Swift 5 to bolster dynamic language interoperability, that allows one to call named types like you'd call functions using a simple syntactic sugar?

Example:
```swift
@(the attribute name) struct SwiftjectiveC 
{
    func showArticles(withTags: [String]) {}
}

let instance = SwiftjectiveC()

instance("Foundation", "Swift")
// This will desugar down to instance.showArticles(withTags: ["Foundation", "Swift"])
```

**Question 2:**  
In Swift, variables are initialized before they are used by a concept enforced by LLVM's optimizer. What is this concept in computer science referred to as?

**Question 3:**  
SIMD Vector and Result types were finally added to Swift in an official capacity in what version?

**Question 4:** <br />
Before Apple's Swift was announced, there already existed a parallel programming language by the same name - who were its developers?

**Wildcard:**  
Before Swift was a popular programming language at Apple, it was also the codename for an Apple-designed processor - which one was it?

### Round 2 — iOS History 101

**Question 1:**  
Auto Layout, long a core component of laying out user interfaces on macOS, didn't arrive on iOS until which major release?

**Question 2:**  
Security is a hallmark feature of iOS, and this specific feature involves placing data in random locations in memory and works alongside ARM's XN (Execute Never) feature to prevent buffer overflow attacks - what is it?

**Question 3:**  
When iOS was still shrouded in secrecy within Apple, how old was the youngest engineer working on iOS 1.0?

**Question 4:** <br />
When the iPhone was first launched on June 29th, 2007 its operating system wasn't yet referred to as iOS until iOS 4. What was its original name?

**Wildcard:**  
Leading the way to haggle all of your friends who appear as green bubbles in your conversations, in which version of iOS did iMessage debut?

### Round 3 — Apple Myth and Lore

**Question 1:**  
Leading up to its March 25th, 2019 "It's Showtime" keynote, Apple live streamed Carplay displaying a six hour ride - where was its end destination?

**Question 2:**  
Steve Jobs, long known as being a master of details, hotly debated _what_ aspect of an Apple Store's bathroom signs?

**Question 3:**  
What was the very first thing _ever_ rendered in a Safari browser, produced by Ken Kocienda during its development?

**Question 4:** <br />
To ensure that a visiting Ross Perot wouldn't think Apple and its employees were too rich for investment - Steve Jobs had himself and Randy Adams hide what objects before he arrived?

**Wildcard:**  
What original Apple employee quickly sold off their 10% share of the company in 1977 for only $800 (which would be worth billions today)?

### Answer Key
<b>Round 1:</b>
1. [@dynamicCallable][5]
2. [Definitive Initialization.][6]
3. Swift 5.
4. The University of Chicago and Argonne National Laboratory.
5. Wildcard: [The Apple A6 and A6X chips.] [7]

<b>Round 2:</b>
1. iOS 6.
2. Address Space Layout Randomization.
3. 18 years old, Scott Goodson.
4. iPhone OS.
5. Wildcard: iOS 5.

<b>Round 3:</b>
1. Cupertino.
2. [The shade of gray they should be colored as.] [8]
3. [A black obelisk!][9] Fun fact, it actually rendered in the wrong direction.
4. [Their Porsche 911s] [10]
5. Wildcard: One of it's oft forgotten co-founders, Ronald Wayne.

[1]: {{ site.url | append:"/WWDC-2015-The-Pregame-Quiz" }}
[2]: {{ site.url | append:"/WWDC-2016-The-Pregame-Quiz" }}
[3]: {{ site.url | append:"/WWDC-2017-The-Pregame-Quiz" }}
[4]: {{ site.url | append:"/WWDC-2018-The-Pregame-Quiz" }}
[5]: https://github.com/apple/swift-evolution/blob/master/proposals/0216-dynamic-callable.md
[6]: {{ site.url | append:"on-definitive-initialization/" }}
[7]: https://en.wikipedia.org/wiki/List_of_Apple_codenames
[8]: https://www.businessinsider.com/steve-jobs-attention-to-detail-2011-10#when-he-was-hospitalized-he-rejected-masks-because-they-were-ugly-8
[9]: https://asciiwwdc.com/2014/sessions/237
[10]: https://www.forbes.com/sites/connieguglielmo/2012/10/03/untold-stories-about-steve-jobs-friends-and-colleagues-share-their-memories/#4932fdc6c584
 