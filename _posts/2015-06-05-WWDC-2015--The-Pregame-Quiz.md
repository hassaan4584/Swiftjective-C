---
layout: post
tags: ["Trivia"]
title: "WWDC 2015: The Pregame Quiz"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Now that WWDC is here, let's start a new annual tradition. It's time for the very first WWDC Pregame Quiz."
image: /assets/images/logo.png
---
Among my favorite entries to the always insightful [NSHipster][1] posts are the annual trivia questions.

They've always been put together in a way that challenges the most embattled iOS developers among us, yet they give the newly minted Cocoa Touch programmer a fighting chance.

As a tribute to those fond memories I have of participating in them, this week I'll take a different approach and instead create one of my own.

Challengers — start your runloops.

### Ground Rules

There are three rounds, and the point break down is as follows:

* **Round 1** –1 point each answer
* **Round 2** - 2 points each answer
* **Round 3** - 3 points each answer

The last question of each round is an optional wildcard question. Get it right, and your team gets **4 points**, _but_ miss it and the team will be **deducted 2 points**.

As with the NSHipster quiz, it's best to write down each answer on paper and then present them. Because, you know, cheating.

Ready?

### Round 1 — Swift Softball Questions

**Question 1:**  
The environment to quickly debug and prototype Swift code that supports markdown and near instant execution that was announced at WWDC 14 is called what?

**Question 2:**<br />
In order to expose Objective-C code to Swift, one must add what to their project?

**Question 3:**<br />
Swift requires the use of parenthesis around _if_ statements, true of false?

**Question 4:**<br />
How many access level modifiers does Swift have?

**Wildcard:**<br />
Swift's logo contains a bird, which is specifically called a ____?

### Round 2 — Bring Your Thinking Cap

**Question 1:**  
Instead of calling methods, Objective-C's model of Object Oriented Programming revolves around what technique?

**Question 2:**  
Swift ensures each variable will be initialized by using [definitive initialization][2] by specifically using which part of the compiler?  
a) Front end  
b) Optimizer  
c) Back end  
d) None of these

**Question 3:**  
To create a variable in Swift that uses unmanaged memory, one uses which type specifier?

**Question 4:**<br />
What controversial technique is used by the Foundation Framework to implement Key-Value Observing?

**Wildcard:**  
The author of Swift's compiler is who?

### Round 3 — Senior Developers Only

**Question 1:**  
Given a valid Swift class, a variable of type Int? will bridge to what in Objective-C code?

**Question 2:**  
In Swift, a weakly referenced variable can be declared as a constant: True or false?

**Question 3 (code challenge):**  
Overload the "+" operator to return a string _x _numbers of times that's comma delimited. Ex: "Hi" + 3 //Hi, Hi, Hi

**Question 4:**  
Aside form AnyObject, there is one protocol that is returned as a type in the Swift standard library. What is it?

**Wildcard:**  
Finish the sentence. At WWDC 14, it was famously declared that Swift is "Objective-C ____ ___ __!"

### Answer Key

<b>Round 1:</b>
1. Playgrounds
2. A bridging header file
3. False
4. 3 — public, internal, and private.
5. Wildcard: A swift — gotcha.

<b>Round 2:</b>
1. Message Passing
2. B — Optimizer
3. UnsafePointer<T>
4. Method Swizzling
5. Wildcard: Chris Lattner

<b>Round 3:</b>
1. It won’t, Swift’s optional value types are not bridged over to Objective-C.
2. False — Weak references must be declared as variables, to indicate that their value can change at runtime. A weak reference cannot be declared as a constant.
3. Code passes as long as it overloads the “+” operator and returns the expected result. Here is a sample that accomplishes this:
```swift
func + (word:String, iterations:Int) -> String
{
    var result:String = word
    for _ in 1..<iterations
    {
        result += “, “ + word
    }
   
    return result
}
//"Hi, Hi, Hi, Hi"
print(“Yo” + 4)
```
4. MirrorType — Used for reflection.
5. Wildcard: “Objective-C without the C.”

[1]: http://www.nshipster.com
[2]: https://medium.com/the-traveled-ios-developers-guide/on-definitive-initialization-54284ef5c96f

  