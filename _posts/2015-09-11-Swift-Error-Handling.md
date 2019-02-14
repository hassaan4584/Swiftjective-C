---
layout: post
tags: ["Swift"]
title: "Error Handling"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Dealing with errors is usually a developer's favorite task to procrastinate against. But it shouldn't be, and even though Swift's inplementation is differnt - it's robust."
image: /assets/images/logo.png
---
Insurance and assurance.

Both ideas are not glamorous, but in tandem they provide stability in the world of programming. On one side stands the notion of providing a fail safe when things so wrong. On the other? A bright light promising that things will be okay, even if the worst comes to pass.

To me — that's error handling. Knowing things could go south but continuing along with confidence. And Swift 2 issues developers a syntactically pleasing way to stay safe in the event of disaster.

### Act I: The 10,000ft View

It's a humble thing, really, programming for your inevitable failure. Unless you are LLVM, Skynet, [or this guy][1] — it's certain that you will not bat 1.000. Be that as it may, we like to think things won't explode spectacularly in our software. But oh , sweet regret, they will.

Swift 2 was gracious enough to provide a robust set of tools with which to handle one's errors in code. Among them are constructs for throwing, catching, manipulating recoverable errors at runtime, and propagating exceptions.

Adding more power to the mix is the fact that Swift's error handling interops nicely with our old friend NSError. Alas, there is a lot here to unpack. Perhaps unsurprisingly, all ideas work together to form one cohesive unit with which to handle errors.

### Act II: ErrorType Protocol

At this point in your Swift endeavors, you are either learning to embrace Swift's [new way of thinking][2] or staying steadfast in your [battle tested ways][3]. If you fall among the former rather than the latter, you'll want to know about the ErrorType protocol.

Swift enforces meaningful errors by representing them as values of types. This design encourages developers to really analyze problems and evaluate _how_ things could go south — and then provide context as to _why_.

To demonstrate, allow me to provide you a mechanism to determine if your weekend is going be NSAwesome or NSStressful:
```swift
enum WeekendError: ErrorType  
{  
    case Overtime  
    case WorkAllWeekend  
}
```
Taking things a bit further, one can harness Swift's strapping enums to provide even greater detail for the unexpected:
```swift
enum WeekendError: ErrorType  
{  
    case Overtime(hoursWorked:Int)  
    case WorkAllWeekend  
}
```
### Act III: Throw

The next logical step to error handling for Swift developers is the act of throwing. In essence, throwing an error indicates that something unexpected took place. Inherently, an exception has been raised which means that execution cannot continue.

Say, for example, you ended up putting in a half day of work in on Saturday (because deadlines, amirite?):
```swift
throw WeekendError.Overtime(hoursWorked: 4)
```
Once an error finds itself executing, a few things could happen:

1. Propagation (good)
2. It's handled in a do-catch (also good)
3. It's treated as an optional value (still alright)
4. Asserting that an error won't occur (playing with fire)
5. Your app crashes. You get a one star review. Nobody downloads your app. You go back to looking at the top 25 downloads in the App Store with longing eyes, letting your dream of occupying the space slowly die inside your cold, bitter heart.

Should you be interested in going with anything other than option 5, please read on:

### Act IV: Handling Errors

As the developer of your app, you are keen about parts of it that could raise an exception. For these murky waters, it's important to identify these regions as such to indicate that program flow can and will change.

**_Propagation_**  
For functions that could produce an exception, it's wise to mark them as such with the throws keyword. It's also a natural fit to use [guard][4] in such scenarios:
```swift
func haveAWeekend(extraHoursWorked:Int) throws  
{  
    guard (weekendHoursWorked == 0 ) else  
    {  
        throw WeekendError.Overtime(hoursWorked: extraHoursWorked)  
    }
    
    print("All clear! Keep preparing for Halo 5:Guardians.")  
}
```
Here, `haveAWeekend(_:)`` is said to be a throwing function. However, this just indicates that the caller should be careful, but it doesn't handle the actual error. So what is one to do?

Well, try, try? and try! again!

Here, `haveAWeekend(_:)`` propagates any errors that it can throw, so it's the callers responsibility to handle them. Variety being the spice of life — there are multiple ways one can approach the problem.

**_do-catch**
The do-catch allows one to execute code and pattern match against any possible error value, a benefit derived from the aforementioned ErrorType:
```swift
do  
{  
    try haveAWeekend(4)  
}  
catch WeekendError.Overtime(let hoursWorked)  
{  
    print("You worked (hoursWorked) more than you should have")  
}  
catch WeekendError.WorkAllWeekend  
{  
    print("You worked 48 hours :-0")  
}  
catch  
{  
    print("Gulping the weekend exception")  
}
```
The do-catch pattern doesn't have to handle every imaginable WeekendError that might occur. Here, the bottom catch gulps every exception that slips through pattern matching.

This doesn't have to be the case, however. It's certainly valid to allow the error to propagate to the surrounding scope which will eventually handle it.

**_try?**<br />
Swift 2 allows for a more succinct way to deal with errors in the form of the try? keyword. Here, the error can be converted into an optional whose value will be nil if an error **is** thrown:
```swift
func hoursOfHaloPlayedThisWeekend() -> Int throws { /*errors, return, etc */}

let haloPlayTime:Int = try? hoursOfHaloPlayedThisWeekend()
```
If `hoursOfHaloPlayedThisWeekend()`` does throw an exception, haloPlayTime will be nil. If that isn't the case, it's assigned the return value. This technique is especially useful for scenarios where one wants to handle errors in a similar fashion.

Take note — the astute developer may have noticed that this variable will actually be an _optional_ Int since its assignment involved the try? keyword. So, use one of Swift's nine (seriously) different approaches to unwrapping it as you see fit.

**_try!_**  
Sometimes, you just want to play with fire. The outside world largely views us programmers as timid introverts — but when we get down, _we get down_. And what better way to show your bravery than to disable error propagation all together?

So should you be looking to show off your bravery, try! is for you. In keeping with Swift's ! syntax, this route lets one wrap the call in a runtime assertion that nothing will go wrong:
```swift
func hoursOfHaloPlayedThisWeekend() -> Int throws { /*errors, return, etc */}
let haloPlayTime:Int = try! hoursOfHaloPlayedThisWeekend()
```
If your judgement is even slightly off, you've just become the brand new owner of a runtime exception that went unhandled. Crash.Burn(yourApp).

### Wrapping Up

And as quickly as it appeared — it vanished. This post wraps up our journey about thoughts on how to write clean code in Swift 2.

It's a good way to go, however, as error handling is a fundamental, if not sometimes tedious, technique to understand when one is creating software. It's a bit paradoxical in nature (coding on the pretense of expecting the unexpected to occur) but it's nice to know Swift handles it with ease.

Until next time ✌️.

[1]: https://twitter.com/clattner_llvm?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor
[2]: {{ site.url | append:"/protocol-oriented-programming" }}
[3]: {{ site.url | append:"/objective-c-in-2015" }}
[4]: {{ site.url | append:"/swift-guard" }}
