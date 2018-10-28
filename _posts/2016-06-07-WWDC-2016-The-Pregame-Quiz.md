---
layout: post
tags: ["Trivia"]
title: "WWDC 2016: The Pregame Quiz"
author: Jordan Morgan
description: "It's our favorite time of the year, dub dub. Let's kick it off with some Apple trivia."
image: /assets/images/logo.png
---
The Second Annual T.T.I.D.G. quiz is here 🎊

If you'd like a quick primer on how this all works or how it got started, feel free to pop over to [the first quiz from last year][1].

Participants — spin up your given NSOperationQueue ⚡️

### Ground Rules

There are three rounds, and the point break down is as follows:

* **Round 1** –1 point each answer
* **Round 2** - 2 points each answer
* **Round 3** - 3 points each answer

The last question of each round is an optional wildcard question. Get it right, and your team gets **4** **points**, _but_miss it and the team will be **deducted 2 points**.

### Round 1 — Swift Softball Questions

**Question 1:**<br />
Which keyword is used to define a constant in Swift?

**Question 2:**<br />
A Swift class can be created without a base class, true or false?

**Question 3:**<br />
The process for querying and calling properties, methods, and subscripts on an optional that might currently be nil is called what in Swift?

**Question 4:**<br />
Values in Swift can be implicitly converted to other types, true or false?

**Wildcard:**<br />
Which University developed and released the lesser know parallel scripting language also known as Swift?

### Round 2 — Bring Your Thinking Cap

**Question 1:**  
What's the name of the popular API that supports the asynchronous execution of operations at the Unix level of the system?

**Question 2:**  
Like other literals, String literals in Objective-C are created by changing the actual code upon compilation, true or false?

**Question 3:**  
The following property _foo_ could be mutated outside of its class, true or false?
```swift 
public class dontOverThinkIt  
{  
    public private(set) var foo: String  
}
```
**Question 4:**<br />
In a popular WWDC 15 session, it was declared that at its heart, Swift is a ______ ______ language even though it can be used as an object oriented one. What was the term used to describe Swift?

**Wildcard:**  
What was the popular term for code written as a result of several layers of unwrapping optionals in Swift?

### Round 3 — Senior Developers Only

**Question 1:**  
What's the name of the design pattern that the Foundation framework uses extensively which consists of grouping a number of private concrete subclasses under a public abstract superclass?

**Question 2:**  
What was the name of the lesser known technique that's being removed in Swift 3 which consisted of passing in a tuple matching a function's formal parameter list?

**Question 3 (code challenge):**  
Given the following variable of type UInt8, write code that would result in its value being set to 0 (zero) without directly assigning it as such:
```swift
var box = UInt8.max  
// Your code  
print(box) //Results in 0
```
**Question 4:**  
As of Swift 2.2, where was the one, and only, place where the Bit type is used in the Swift standard library?

**Wildcard:**
What was the first (and eventually accepted) proposal for the Swift programming language submitted from the community?

### Answer Key

<b>Round 1:</b>
1. let
2. True
3. Optional Chaining
4. False
5. Wildcard: The University of Chicago.

<b>Round 2:</b>
1. G.C.D., or grand central dispatch.
2. False, they are compiled as constants in its containing executable.
3. False
4. A protocol oriented programming language.
5. Wildcard: The pyramid of doom!

<b>Round 3:</b>
1. Class Clustering
2. Tuple splatting.
3. The variable box is initialized with the max value a UInt8 can hold (11111111 in binary, or 255). Adding 1 to box using the overflow addition operator pushes its binary representation over what a UInt8 can hold, which means that it overflows beyond its bounds. The remaining value within the bounds of UInt8 after the overflow addition is 00000000 in binary, or zero:
```swift
//Box equals 255, which is the maximum value a UInt8 can hold
var unsignedOverflow = UInt8.max
box = box &+ 1
print(box) //0
```
4. It was used as the index for `CollectionOfOne`. The Bit type will be removed in Swift 3.
5. Wildcard: [To allow most Swift keywords to be used as an argument label][2].

[1]: {{ site.url | append:"/wwdc-2015-the-pregame-quiz" }}
[2]: https://github.com/apple/swift-evolution/blob/master/proposals/0001-keywords-as-argument-labels.md
