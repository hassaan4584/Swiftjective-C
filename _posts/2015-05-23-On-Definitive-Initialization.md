---
layout: post
tags: ["Swift"]
title: "Definitive Initialization"
author: Jordan Morgan
description: "Swift goes to great lengths to ensure values are initialized before they are used. The reasons how vary from API design to the way LLVM works - let's chat about this process called definitive initialization."
image: /assets/images/logo.png
---
Swift might as well proclaim that phrase within its documentation. Optionals and the enforcement of assignment in the name of safety is a departure from Objective-C. If there is not a value for a variable — you make one.

And it can't be avoided. Using data-flow algorithms *LLVM scours one's codebase to ensure variables have been assigned a value. It barks if they don't. The house always wins.

That's the essence of definitive initialization — our topic this week.

### Definitive What?

We programmers tend to be packaged in two different flavors when learning something new:

* Those who can read the docs and have the lightbulb immediately turn on
* And, those who read the docs and get a small flicker of the lightbulb. Then they view code of the given concept in context. Alas, the lit lightbulb doth beckon.

I tend to be the latter rather than the former. So, when I first read about definitive initialization I opted to learn from telling examples such as this:
```swift
class DIBApp  
{  
    var name:String  
    init(aName:String)  
    {  
        name = aName  
    }
    
    func doWork(){}  
}

//Non-nullable, and also uninitialized at this point  
var anApp : DIBApp

if aBool  
{  
    anApp = DIBApp(aName: "Spend Stack")  
}  
else  
{  
    anApp = DIBApp(aName: "The Halo Timer")  
}

//No warnings or compiler errors here  
anApp.doWork()
```
Swift's compiler can analyze this code and know that anApp will have a value. LLVM is looking to prove to itself that the doWork() function call will not attempt to use any uninitialized memory.

That's proven quite easily here, as the assignment of anApp is the result of a binary decision structure. One of the two outcomes will occur. No dark timelines there.

### But Wait

You've read enough. Lazy-loading is never an issue in Swift, you say. You've one foot out the door.

Stop — and stay with us here for a moment.

This may come as a shock, but LLMV is not Skynet. This means it can't make certain assumptions about initialization. In fact, it was purposely tuned _not to_.

Example:
```swift
class DIBApp  
{  
    var name:String  
    init(aName:String)  
    {  
        name = aName  
    }
 
    func doWork(){}  
}

//Non-nullable, and also unitialized at this point  
var anApp : DIBApp

if 1 < 2  
{  
    anApp = DIBApp(aName: "Spend Stack")  
}

//ERROR: anApp used before being initialized   
anApp.doWork()
```
Why is this a problem? Unless the rules have changed since they were established in Babylon during the 2nd millennium BC, one is most assuredly always less then two.

In addition, Swift is a young but advanced programming language. We've landed spacecraft on a moon with coding constructs far less capable.

So — why can't Swift do math?

### K.I.S.S.

LLVM could have been developed to handle a scenario that's as trivial as the previous one. The reason it wasn't, however, is because it couldn't be developed to _always_ be right with those assumptions.

So had they made it be mostly right, then it could always be wrong.

The predicates of the previous _if_ statements are not tracked at all. Imagine if the evaluation were not so simple:
```swift
if foo() && baz()   
{  
    anApp = Init(aName:"Who cares b/c this won't compile")  
}
```
Anything could happen inside foo() and baz(). If Object Oriented Practices are violated, they could do too much. Perhaps they do nothing.

This leads to the halting problem — which is a computer sciencey way of saying "Given this code, who knows when this program will end. And hey, maybe it wont."

And so it is — Swift wisely opted to be predictable and simple. Less is more.

It could have been developed to be mostly right, but when it would be wrong — one would be left with a runtime exception and a few debug sessions wrought with ambiguity. Thus, definitive initialization is used.

### Bring Your Friends

This is nothing new, however. In fact, Java and C# have been employing such a technique years before Swift even crossed Cupertino's collective minds.

Swift, however, uses a mashup of a few different approaches to definitive initialization. Consider how this particular topic of safety (ensuring variables initialization) is handled in other programming languages.

### You could be like the black sheep partying son, C.

C has no rules when it comes to the topic of variable initilization. It gets blackout drunk on the weekend and takes a freshman out on a date during finals. C lives life in the fast lane and hopes you don't make a mistake.

Because if you do — kapow. At runtime.

This is inherently unsafe, and by far the most error-prone technique. No matter — you still _can_ do this in Swift. One may wield such power via the UnsafePointer API:
```swift
//Like C's Type * const *  
UnsafePointer<String> var yolo
```
Here, you are ARCless and alone. Be sure to free that memory back up.

### You could be the more behaved, yet still rebellious Objective-C

Objective-C has a healthy regard for rules, but only to a certain point. It still goes out on the weekend and has friends in shady places. Yet, it still finds itself in the pews on Sunday morning.

Objective-C lets the compiler assign default values, thus helping to ensure variable initialization. Whether you were aware or not, Objective-C was safely supplying zero values all over the place to iVars:
```swift
//Assigned to 0 for you  
NSUInteger anInt;
```
So, safer — yes, but still far from Swift's stance. What happens when a variable is encountered who has no legal default initialization value? Say, a non-nullable reference to a class? You get nothing, which is an issue.

Further, this pattern can lend itself to less than optimal readability for the "next guy." Assigning 0 doesn't always make sense as a default value. Further, -1 is sometimes an intended sentinel value.

As you might have already guessed, Swift again has borrowed from this technique as well. For instance, nil is certainly, 100% of the time the correct value to assign to optionals and implicitily unwrapped optionals types.

So, you get that for free. But lastly…

### You could be the straight-A church volunteering child, i.e. most functional languages

Play by the rules. Lights off at 8:00 p.m. sharp. Because we wouldn't want to be late volunteering at the animal shelter tomorrow, would we?

Without question the most restrictive approach is requiring a value upon declaration. It's a common misconception that this is how Swift actually works, and that's simply not true.

If it were, this Swift code would not compile:
```swift
var anInt:Int
```
Yet it does — under the right circumstances. And the circumstances being?

That after Swift's analysis takes place from the optimizer, it knows that anInt will be assigned to before it's used. This type of approach to variable initialization is of course safe, but only safe in the way that never leaving your room is safe.

In short — it's just a little too much. It enforces a very strict style of programming, one which the programmer is forced to adopt.

### Wrapping Up

Swift has taken an imaginative approach to variable initilization. Using definitive initialization, mixed with the benefits of the other approaches, the developer is left with a mixed bag that's useful in solving many problems without almost any of the cons.

While it takes some getting used to, especially due to my love affair with Objective-C that I've enjoyed for so long, I can say that I've come to appreciate it. It's smart, it's safe, and lets one program in a flexible manner without feeling too UnsafePointer.

Until next time ✌️.

> _LLVM really has 3 parts to its tool chain. Those are the front end, the optimizer, and the back end. The optimizer is what enforces definitive initialization by taking the LLVM IR from Clang (the front end component).
