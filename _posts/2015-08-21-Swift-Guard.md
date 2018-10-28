---
layout: post
tags: ["Swift"]
title: "Guard"
author: Jordan Morgan
description: "More than an _if_ statement, this simple construct makes code safer and readable. But when do you use one or the other?"
image: /assets/images/logo.png
---
Clarity. There is nothing quite like it. When all distractions are removed and only the relevant remains, decisions are made effortlessly and plans unfold naturally.

And so it is, I strive to create code that promotes clear intent and easy understanding. This week, we'll spend time looking at a brand new member to Swift 2's repertoire that lives solely to promote readable code.

Meet the _guard_ statement.

### Swift's Bouncer

I'll provide a friendly warning to any of my followers on [Twitter][1] right now, I'll be fawning over the guard statement for some time. For good reason, too — because developers all over the world have been stopped in their productive tracks at some point by reading code that just doesn't make sense.

Paralysis by analysis. It's frustrating.

What guard gives us to ease those scenarios is a singular objective — if you don't have what I need, _get out_. Much like one cannot show up to concert without a ticket, guard patiently sits in your compiled code waiting to stop any ill intentioned values in their collective tracks.

### The Syntax

Guard's academic definition goes something like this:

"A construct meant to transfer execution out of scope if one or more conditions are not met."

It's the _if _statement with a bit more gusto and hair on his chest. And his makeup looks like the following:
```swift
guard (condition I want to be true) else  
{  
    //return, break, continue, or throw  
}
```
With one pseudo code sample, the astute developer can infer a few advantages to the guard statement.

* It's name explicitly communicates the code's intent
* It's use case is not wide spread, but direct and purposeful
* Other developers who maintain code authored by you who run across _guard _will immediately understand what is happening

Guard even allows one to crash the entire show with functions decorated with the @noreturn attribute:
```swift
func executiveOrgEmployee(index: Int) -> Employee  
{  
    switch index  
    {  
        case 0:  
        return mockData.CEO  
        case 1:  
        return mockData.COO  
        default:  
        fatalError("If this happens, we're in trouble")  
    }  
}
```
### Hello Clarity

If you've been developing in Cocoa Touch for more than twenty minutes or have completed the ubiquitous **_Hello World!_** tutorial, you've most likely implemented a UITableViewDelegate. It's just a universal truth. And it's yielded code much like this:
```swift
func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath)  
{  
    if (indexPath.row > myDatasource.count)  
    {  
        return  
    }  
}
```
From day one, developers are taught (or learn the hard way) that `indexOutOfBounds` is not your friend.

Thing is — there isn't anything wrong with that code. And that's why I love guard, it makes this code just that much better.

Replacing code like this with guard is trivial — yet another advantage to its brief but welcome existence:
```swift
func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath)  
{  
    guard (indexPath.row < myDatasource.count) else  
    {  
        return  
    }
    
    //Come in, waters fine  
}
```
Guard flips the script and allows the developer to check for things they **do** want instead of things they don't. Think of all the times this happens, and I mean really think about it for a moment. Because this happens _all the time:_
* Is that text field blank when entering a username?
* Is that indexPath going to cause an indexOutOfBounds exception?
* Is that boolean flag on?
* Do we have internet connectivity?

It's only by people who are much more talented at software engineering than I who make these realizations and say, "Hey, we can make this common situation in Swift a little better." Thus, guard was born.

### Optionals

Guard has more umph to it than just being a pretty face in your code. One can easily employ both optional unwrapping alongside pattern matching for one beautiful line of code.

Suppose we have a function whose signature expects an optional String. Let's also imagine we have an employee in our system whose [last name is maybe, but seriously, Null][2].
```swift
private func printRecordFromLastName(userLastName: String?)   
{  
    guard let name = userLastName where userLastName != "Null" else  
    {  
        //Sorry Bill Null, find a new job  
        return  
    }
    
    //Party on  
    print(dataStore.findByLastName(name))  
}
```
This is a fantastic way to handle such a scenario. The guard statement is calling out the conditions that we intended to operate on and backs out of trouble if they aren't there. As long as the value of the type housed in the guard statement conforms to the BooleanType protocol, guard will clean house.

It's a souped up if statement. It's a safer and less aggressive assert. It's a coherent sentinel value.

However — the real power here comes _after_ the guard statement. The optional value of userLastName has been unwrapped for the programmer and is now guaranteed to be safely accessible. If you've tipped your toes in Swift, you know it enforces a [zero tolerance stance][3] when it comes to accessing nil references.

### Wrapping Up

I love guard. Straight up. I've noticed several times in my day to day on an iOS 8 project where it would be oh so useful. I simply can't wait to use it more.

And why not? This is what it's all about as a developer. Creating code that's forward thinking. Code that isn't clever — code that's easy to get and pick up. Excuse the enthusiasm, but it's a small tweak that makes a big difference. 

Until next time ✌️.

[1]: http://twitter.com/jordanmorgan10
[2]: http://stackoverflow.com/questions/4456438/how-do-i-correctly-pass-the-string-null-an-employees-proper-surname-to-a-so
[3]: {{ site.url | append:"/on-definitive-initialization" }}