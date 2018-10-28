---
layout: post
tags: ["Swift"]
title: "Defer"
author: Jordan Morgan
description: "More than an _if_ statement, this simple construct makes code safer and readable. But when do you use one or the other?"
image: /assets/images/logo.png
---
I put a King's ransom on being lazy yet precise.

Honestly — I think it's a mindset that lends itself to smart and sophisticated architecture of code. So when Swift 2 batted its loving eyes inside of Xcode for the first time, I found myself most taken by a few choice enhancements:

- Defer
- Guard
- Error Handling

There is a certain beauty to code that's constructed in a meaningful yet powerful fashion. For developers, it's poetry in motion. So this week we begin Part 1 of 3 on learning about Swift's syntactical sugar — the defer statement.

### Procrastinate

Procrastination gets a bad wrap, and undeservingly so in the world of programming. Those who participate in the act prolong tasks that ultimately just need to get done. At the same time, procrastinators mean well and will get any and all meaningful work finished at the midnight hour.

Take, for instance, me.

I post an article examining something in the vast world of Cocoa Touch, usually on Saturday, around 5 C.S.T. And yet- here I am at 2:30 getting it done. _tskTsk.aiff_

Defer agrees with this lifestyle, which is perhaps why it resonates so well with me. It knows that something must be done, but seriously — _it can be done later_. Just so long as it's done. We've all seen things ripe for deferment:

* Closing that file stream
* Cleaning up that database connection
* Freeing up manually allocated memory

They are what they are — chores. Unfashionable things one must do.

### Know Your Role

As with any new toy in programming — be sure to read the manual to provide the best care and experience for the new construct. In this case, defer only wants to help you master error handling in a way that makes sense for both the author and future maintainers of the code.

Consider this pseudo Swift:
```swift
func openFile(fileName:String)  
{  
    let myFile = openFile(name:fileName)
    
    while let fileLine = file.readline()  
    {  
        print(fileLine, appendNewline: true)  
    }
    
    CloseFile(myFile)  
}
```
Can you spot the housekeeping? That would be none other than closing the file. Hell or high water, one must close it.

To better communicate this intent, one can use defer:
```swift
func openFile(fileName:String) throws  
{  
    let myFile = openFile(name:fileName)
    
    defer  
    {  
        CloseFile(myFile)  
    }
    
    while let fileLine = file.readline()  
    {  
        print(fileLine, appendNewline: true)  
    }  
}
```
Defer will now ensure that CloseFile(_:) executes no matter what good or evil transpires within scope.

Notice the throws keyword was tacked on the function's signature as well. Both the defer statement and error handling work together to pioneer safe programming. Think of defer as a finally statement in other popular languages.

More on error handling next week.

### Speaking of Scope

Defer executes right before the current scope's execution has transpired. That means one can make intelligent decisions early on about how code will be written. This a rule with no associated exception — defer executes as the last part of the current scope.

The meaning of the former conclusion seems to be lost on a few developers. Put bluntly, I've read a few choice examples authored much like this:
```swift
for _ 0...3  
{  
    //Work
    
    defer  
    {  
        //Things went south, close up shop   
        close(file)  
        break  
    }  
}
```
Much like the first drink at the bar, intentions here are just and pure. But in reality, things are going south much too quick.

In particular, the break statement is what, ironically enough, breaks the previous code. The reason being is that the defer statement cannot shift control out of the current statement. Doing so breaks what defer stands for and negatively impacts how it's intended to work.

Given this realization, these fine citizens shall not pass in defer's world:

* break
* return
* Throwing an error

### L.I.F.O.

There are things that can be done in the world of programming that aren't necessarily meant to. The reason they exist?

For the edge case. The edge case sits silently in a corner, only showing itself to wreck what was otherwise sound logic and seemingly well architected code. Sometimes hacks and self shaming ensue on the developer's end to fix said edge cases.

So, when I tell you that defer statements internally push themselves onto a stack, I tell you for the sake of completeness.

As such, all defer statements are executed in reverse order. You may know this as L.I.F.O., or opposite of what a queue does. For the sake of academia, I present unto you…

The contrived example:
```swift
func DeferAllTheThings()  
{  
    defer  
    {  
        print("Defer A")  
    }
    
    defer  
    {  
        print("Defer B")  
    }
    
    defer  
    {  
        print("Defer C")  
    }  
}

//C, B, A  
DeferAllTheThings()
```
There are use cases for this type of behavior, sure. And it makes sense they are implemented in such a fashion, staying quite literally true to their name.

But in reality, such power should be used with caution. Not doing so upends what defer does so very well: communicating intent and promoting maintainability.

### Wrapping Up

There is a quote referenced quite commonly throughout the development community by Donald Knuth. In essence, it proclaims code should lean towards readability more than anything else. It asserts that maintainability is where true value is found in the software development industry.

And should you fall on that side of the fence, as I tend to, defer brings something quite nice to the proverbial Swift table. It expresses code thoughtfully while handling errors and clean up efficiently. 

Until next time ✌️.