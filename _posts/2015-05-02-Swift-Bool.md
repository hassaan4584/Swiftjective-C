---
layout: post
tags: ["Swift"]
title: "bool"
author: Jordan Morgan
description: "The boolean variable, perhaps the most simple implementation in Computer Science - or is it? Swift's version can do more than just spit out 0 or 1."
image: /assets/images/logo.png
---
Truth.

Boolean types in computer science represent such a simple meaning. Yet, as developers in Cocoa Touch, we've had a multitude of ways in which to express it.

Bool. bool. Boolean. NSCFBoolean. boolean_t.

There were macros for macros when assigning a boolean value in Objective-C. Swift, though not unexpectedly, has choosen a different path. This week — let's take a peek behind the curtain of swift's boolean type — Bool.

### The Elevator Pitch

Because Bool is a named type and implemented in the swift standard library using a structure, their behavior can be extended in ways not typically associated with value types. In fact, all of swift's "primitives" are named types and, like Bool, they can twisted, warped, and turned to your heart's content.

So, should you find yourself wanting to confuse coworkers via a Bool extension:
```swift
extension Bool  
{  
    var isTrue:Bool {return false}  
}

//Later on, somewhere else in your project  
let someBoolValue = true

//Begin coworker confusion  
if someBoolValue.isTrue  
{  
    //Haha NOPE  
}
```

Swift will certainly allow you to do so. You devil.

You are more than welcome to enjoy extensions, instance methods, computed properties, and protocols using a Bool. In fact, Bool happens to conform to 6 protocols out of the box:

* BooleanLiteralConvertible
* BooleanType
* Printable
* Equatable
* Hashable
* Reflectable

### Init()

As I mentioned in my discussion over [Int][1], an Init() method is not something one usually associates with a primitive type. Bool, however, houses logical initializers to continue your quest for truth:
```swift
let defaultInit = Bool() //False  
let literal = Bool(booleanLiteral: 1 > 0) //True
```
Exactly what you might have expected.

What's perhaps pertinent to notice is what makes a Bool type operate in lockstep with true and false values. This would be one of the six protocols mentioned earlier — in particular BooleanLiteralConvertible.

BooleanLiteralConvertible only asks that you supply a means with which to initialize a given type from a boolean literal:
```swift
//Create an instance initialized to value.  
init(booleanLiteral value: [BooleanLiteralType][2])
```
Given this, we can convientienly reject our own realities and substitute it with one in which we might prefer:
```swift
class NFLTeams:BooleanLiteralConvertible  
{  
    var teamName:String
    
    init(_ teamName:String)  
    {  
        self.teamName = teamName  
    }

    internal required init(booleanLiteral value: Bool)  
    {  
        self.teamName = value ? "St.Louis Rams" : "Who Cares"  
    }  
}

// Initialize the best NFL team from a boolean literal  
let initBestNFLTeam:NFLTeams = true

// L.A. Rams  
println(initBestNFLTeam.teamName)
```
Ah, finally.

### For Instance

Bool gets straight to the point in terms of its instance variables and functions. Everything you need, nothing you don't.
```swift
let instanceBool = true

//Raw value — true  
instanceBool.boolValue

//"true"  
instanceBool.description

//1  
instanceBool.hashValue

//Returns a MirrorType that reflects Bool  
instanceBool.getMirror()
```
Bool has more than enough to not only determine whether or not something is true or false, but also get fancy with reflection. Again, and I can't stress this enough, it's because Bool is more than just a simple type behind the scene.

Even better, with only four total instance functions and members, one could truthfully boast to coworkers that they have successfully memorized an entire swift type in less than fifteen minutes.

### Wrapping Up

Bools in swift are straight forward, as well they should be. While there are other discussions on the many ways boolean values manifested themselves in Objective-C, it is quite refreshing to know that what you see is what you get in this case.

Until next time ✌️.

[1]: {{ site.url | append:"/swift-int" }}
[2]: http://swiftdoc.org/global/alias/
