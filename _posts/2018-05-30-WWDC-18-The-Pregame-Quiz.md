---
layout: post
tags: ["Trivia"]
title: "WWDC 2018: The Pregame Quiz"
author: Jordan Morgan
description: "iOS 12 beckons, but before it does - let's quiz it up over iOS versions of yesterday."
image: /assets/images/logo.png
---
It's Christmas time for iOS engineers the world over. Mr.Cook and friends are a little less than a week away from pulling the curtains off of iOS 12. Will we see vast improvements, or the oft rumored "maintenance" release?

Time will soon tell, but until then it's time for the fourth annual T.T.I.D.G. WWDC Pregame Quiz!

If you'd like a quick primer on how this all works or how it got started, check out the first three quizzes from [2015][1] ,[2016][2] and [2017][3].

Participants — time to add the quiz operation to your queues⚡️!

### Ground Rules

There are three rounds, and the point break down is as follows:

* **Round 1** – 1 point each answer
* **Round 2** - 2 points each answer
* **Round 3** - 3 points each answer

The last question of each round is an optional wildcard question. Get it right, and your team gets **4** **points**, _but_ miss it and the team will be **deducted 2 points**.

### Round 1 — Swiftly Answered

**Question 1:**  
This technique, introduced in a WWDC 15 session, declared that Swift was the industries first _what_ oriented programming language?

**Question 2:**  
On June 2nd, 2014 — what app became the first publicly available app written in Swift?

**Question 3:**  
What's the name of the instance method that's **not** possible to use in pure Swift classes/objects that NSObject uses to invoke objc_msgSend and allow for dynamic method resolution?

**Question 4:**

Which new typealias introduced in Swift 4 extended support of archival and serialization to struct and enum types and enables type-safety for serializing to external formats such as JSON and plist?

**Wildcard:**  
During WWDC 14 when Swift was unveiled, what was the _very first_ public Swift string variable set equal to during its inaugural demo introduction by Chris Lattner?

### Round 2 — iOS' & its Tools Storied History

**Question 1:**  
What framework, added in iOS 5, gave rise to the popularity of photo editing apps by exposing a powerful set of built-in filters for manipulating video and still images?

**Question 2:**  
It's well known that Core Data, Keyed Archiver and User Defaults allow for persistency on iOS. What other persistency option is available by default on the platform?

**Question 3:**  
What CLI, originally released with Xcode 6 and housed within xcrun, allows one to perform various tasks on the iOS simulator, such as recording videos and opening URL schemes?

**Question 4:**

Developers sometimes crash their app in a controlled manner during development by invoking the abort(); function, but there is also a little known intrinsic function that generates a machine-specific trap instruction. What is it?

**Wildcard:**  
This ridiculously long initializer, clocking in at 202 characters, is found within what Apple framework:

```swift
initWithEnableFan:  
enableAirConditioner:  
enableClimateControl:  
enableAutoMode:  
airCirculationMode:  
fanSpeedIndex:  
fanSpeedPercentage:  
relativeFanSpeedSetting:  
temperature:  
relativeTemperatureSetting:  
climateZone:
```
### Round 3 — The Random Apple Ones

**Question 1:**  
This game is a now a triple A blockbuster shooter, but it was originally announced at MacWorld in 1999 and was set to release on the platform as a third person action game. What game is it?

**Question 2:**  
Steve Jobs infamously said that _what_ was a "sweet solution" for developing on the iPhone before the advent of the App Store at WWDC 07'?

**Question 3:**  
The iPhone's revolutionary multitouch interface was prototyped by a team, colloquially referred to as the ENRI group, at Apple in their abandoned user-testing lab at 2 Infinite Loop — what was their original mission statement?

**Question 4:**

John Carmack, long a pioneer of the games industry, went toe to toe with Steve Jobs in advocating that which framework should be adopted as the Mac's 3D Graphics API?

**Wildcard:**  
Though the iPhone's touchscreen has made the paradigm commonplace in today's world — Eric Arthur Johnson is believed to have invented the world's very first touch screen as an engineer at England's Royal Radar Establishment in 1965. What instrument did he create a touchscreen for?

### Answer Key
<b>Round 1:</b>
1. [A protocol oriented programming language][4]
2. The WWDC App, this was confirmed in their Platforms State of the Union address during WWDC of that year.
3. Good ol’ performSelector:
4. Codable
5. Wildcard: 
[The variable “s” was declared as a string set equal to “Hello WWDC!”, showing off Swift’s vastly superior string interpolation API.][5]

<b>Round 2:</b>
1. [Core Image][6]
2. SQLite
3. [simctl][7]
4. __builtin_trap();
5. Wildcard: [SiriKit][8]

<b>Round 3:</b>
1. Halo
2. [Web Apps][9] (Yikes)
3. To “Explore new rich interactions”, hence ENRI
4. [OpenGL][10]
5. Wildcard: An instrument to help operators improve handling air traffic control.

[1]: {{ site.url | append:"/WWDC-2015-The-Pregame-Quiz" }}
[2]: {{ site.url | append:"/WWDC-2016-The-Pregame-Quiz" }}
[3]: {{ site.url | append:"/WWDC-2017-The-Pregame-Quiz" }}
[4]: http://asciiwwdc.com/2015/sessions/408
[5]: https://www.youtube.com/watch?reload=9&v=MO7Ta0DvEWA
[6]: https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS5.html#//apple_ref/doc/uid/TP30915195-SW62
[7]: {{ site.url | append:"/ios-simulator-power-ups" }}
[8]: https://developer.apple.com/documentation/sirikit/insetclimatesettingsincarintent/2102611-init
[9]: https://daringfireball.net/2007/06/wwdc_2007_keynote
[10]: https://news.ycombinator.com/item?id=17067129