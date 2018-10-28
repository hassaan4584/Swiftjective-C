---
layout: post
tags: ["Swift"]
title: "Attributes in Swift"
author: Jordan Morgan
description: "Attributes can keep code clean and conscise without much effort. Today, let's cover some common, and uncommon, use cases."
image: /assets/images/logo.png
---
I think we've all been there. We've just picked up programming, or we start learning a new language where things are foreign to us — and we happen across some code. We may not understand it, but we think it works. So, we just take it in good faith and continue on, none the wiser.

This used to be my approach when incorporating Swift's attributes into my own work. [Swift supports a robust variety of attributes,][1] and when browsing Github repos — we may happen across one or two we don't recognize. If you're like me, you might jot it down to Google and then be on our way.

No more! This week, we take a peek under the curtain at what attributes are, what they do and some fun ones to know about 💫. Lets.begin()

### Why use Swift's attributes?

Attributes help developers improve the efficiency of their codebase, without sacrificing the quality of their code. It makes for code that's easier to read, ultimately easier to maintain and hopefully safer to use.

I like to think that efficiency is always at the forefront of my development. And really — that's what a lot of these attributes can bring to the table, some good ol' fashioned efficiency.

### Groundwork
At the expense of starting by providing a boilerplate definition, let's start by providing a boilerplate definition 🤓.

I think it's requisite to quickly hit on what exactly an attribute is. And really, all a Swift attribute is/does is provide more information about a either type or declaration. This information can dictate everything from compiler warnings to how something is handled in memory.

No matter the flavor, each one is preceded by an "@" symbol. In addition, declaration attributes may also accept arguments inside of enclosing parentheses.

So, put simply:

```swift
@attributeName

//Or with arguments…  
@attributeName(arguments)
```
Let's look at some examples from the front lines.

### @available(args)

If there was a Swiss Army Knife version of a Swift attribute, it would be @available(). Flexible as it is powerful, you may find it indispensable if your daily duties revolve around managing or releasing an API. With it, one can indicate API naming changes, platform availability and more.

Consider an object from a metaphorical API that serves up blog posts:

```swift
class BasicPost {}
```
Consumers of our API have long enjoyed using the BasicPost class, though imagine we've fielded several requests for a more honed in object that represents a technical blog post, much like the one you're reading now. So for version 1.2, we introduce it:

```swift
class TechnicalPost {}
```
Now, to make our documentation complete, our code sensible and our API consumers informed we could take advantage of @available() to make its presence known:

```swift
@available(*, introduced: 1.2)  
class TechnicalPost {}
```
Now, consumers can be quickly informed about the TechnicalPost object's introduction and availability. This particular attribute can accept several arguments, but the first one always indicates the intended platforms. The remaining arguments supported can be supplied in any order.

It can also take advantage of a wildcard. In this case, the wildcard is the asterisk you're seeing as the first argument — which communicates that on all platforms the API is used on, this class was first introduced on version 1.2 (represented by the second argument).

That's neat, but also extremely broad. Thankfully, we can focus it in even more with a shorthand syntax:

```swift
@available(iOS 10.0, macOS 10.12)  
class TechnicalPost {}
```
Much better! If I'm new to our fictional API, I can clearly see when this class can be used even if I don't know much about the attribute itself.

But, if left as is, you'll also receive a compile time error. Why 🤔?

Because of Apple's knack for introducing new platforms, we've got to enforce our code to account for that — and so we include the wildcard as the last argument to signify that this code is available for the provided platforms, and any potential future platforms.

So if Apple makes the IoT connected Apple Toaster**©** — you're all set:

```swift
@available(iOS 10.0, macOS 10.12, *)  
class TechnicalPost {}
```
Now our code is already set for the next big thing that needs a splash of Apple's operating systems 🎉. For the here and the now though, Apple has provided us with an enumeration representing each platform as they exist today:

* `iOS`
* `iOSApplicationExtension`
* `macOS`
* `macOSApplicationExtension`
* `watchOS`
* `watchOSApplicationExtension`
* `tvOS`
* `tvOSApplicationExtension`

Before we move on from this one let's consider another commonality. With our recent changes, our API has taken off, and iOS developers the world over have entrusted us with serving them up with lovely bits of JSON that represent technical blog posts.

As such, we've no need for the original class anymore. I think it's time we deprecate it:

```swift
@available(*, deprecated: 1.3)  
class BasicPost {}
```
Now it becomes clear how useful the wildcard argument can be, as in one (ahem) _swift_ move we've deprecated BasicPost on all platforms.

Further, if we wanted to keep it around but a bit refactored, we could've even provided notice of an API naming change. Courtesy of a technique I caught from Apple, we could pair it with an unavailable argument and a typealias to make things even easier on consumers:

```swift
//From an earlier API version  
class BasicPost {}

//From a new API version, where we renamed it for whatever reason  
class BaseTechnicalPost {}

@available(*, unavailable, renamed: "BaseTechnicalPost")  
typealias BasicPost = BaseTechnicalPost
```
I personally love this, because for my money — the obvious code is always the best code.

This attribute has even more tricks, with support for arguments specifying some code completely obsolete, a message to provide in conjunction with a warning or error and more.

### @discardableResult

If you've worked in a mature, legacy codebase it should come as no surprise that some functions do a bit too much.

Perhaps there is that one function hanging around that's been added to and manipulated since the early 90s, and due to no fault of its own — it might do 124 important things that need to happen when the software starts (access a database, setup a cache, initialize some sign in process — the scenarios are endless).

And so times go on, because someday, you'll convince the product manager to let you come back and refactor it.

Right 😄. _Right 😅!?_ Right 😐:

```swift
let someUnusedVarBecauseIHaveToCallThisOldInsaneFunction = anOldInsaneFunction()
```
Be that as it may, clang will pour salt on the wound here because even though we've got to invoke this function for some outlandish coupling reason — we now have an unused variable to show for it as well. Talk about getting kicked while you're down 🙃.

This is where the @discardableResult attribute can help, as it tells the compiler that the result of the function may be unneeded. It also kills the warning at compile time:

```swift
@discardableResult func anOldInsaneFunction() -> String  
{  
    //Bunch of business logic occurs  
    return ""  
}
```
Now, the code above which invokes the said function will stay there only as a relic of your past software engineering mistakes — but it will do so without providing an error. Baby steps!

For a little syntactical sugar, at least in my opinion, one could make the state of affairs even more obvious by simply assigning to a _ :

```swift
_ = anOldInsaneFunction()
```
Look — it is what it is. Sometimes, there are functions or architecture in software development we can't directly control or fix, and this attribute makes that situation a little bit better.

### @autoclosure

Let's bring things home with another nifty attribute that can add a little syntactical sugar. The @autoclosure attribute can allow one to automatically wrap a closure that's supplied as an argument. As the closure doesn't take any arguments itself, it'll return the actual value of the expression that's wrapped within it.

It sounds a little weird, but it's easily understood when you come across one. All we're really talking about here is the ability to get an expression to automatically become a closure. If you've spent some time adding unit tests to your project, you've likely come across this attribute several times already.

Assume we'd like to write a simple test for a class like so:

```swift
class Programmer  
{  
    var pay:Int
    
    init(withPay pay:Int)  
    {  
        self.pay = pay  
    }
    
    func applyRaise(by amount:Int)  
    {  
        self.pay += amount  
    }  
}

class ProgrammerTests: XCTestCase  
{  
    func testPayRaise()  
    {  
        let devsPay = 50000  
        let raiseAmount = 25000  
        let expectedSalaryPostRaise = devsPay + raiseAmount

        let aDev = Programmer(withPay: devsPay)  
        aDev.applyRaise(by: raiseAmount)

        XCTAssertEqual(expectedSalaryPostRaise, aDev.pay, "Unexpected salary after raise was applied.")  
    }  
}
```
The first two parameters of the XCAssertEqual are both closures that take in a generic expression. While the function's signature can look a little intimidating, take note of the first two parameters that are taking advantage of @autoclosure:

```swift
func XCTAssertEqual(_ expression1: @autoclosure () throws -> T?, _ expression2: @autoclosure () throws -> T?, _ message: @autoclosure () -> String = default, file: StaticString = #file, line: UInt = #line) where T : Equatable
```
Since the @autoclosure attribute is supplied, invoking the function is quite readable and trivial to write. We can either pass the closure with something as simple as a value (as we did in our previous example) or with a bit more logic, and each one is syntactically sensible:

```swift
class ProgrammerTests: XCTestCase  
{  
    func testPayRaise()  
    {  
        let devsPay = 50000  
        let raiseAmount = 25000

        let aDev = Programmer(withPay: devsPay)  
        aDev.applyRaise(by: raiseAmount)

        XCTAssertEqual(aDev.pay + raiseAmount, 750000, "Unexpected salary after raise was applied.")  
    }  
}
```
Take note that when the first argument is supplied, it reads much more like an addition operation than it does a closure:

```swift
XCTAssertEqual(aDev.pay + raiseAmount, 750000, "Unexpected salary after raise was applied.")
```
…versus what it might look like without the @autoclosure attribute:

```swift
XCTAssertEqual({  
    return aDev.pay + raiseAmount,  
}, {  
    return 75000  
}, "Unexpected salary after raise was applied.")
```
As you can see, passing a fully qualified closure (in terms of syntax) — it's a bit much to take in. Plus, that's a compounded problem if one can't use a trailing closure as the last argument.

So with @autoclosure, that's essentially what we mean when we say that the closure _returns_ _the actual value_ that's wrapped inside of it. You might even say the parameter became a closure…automatically, thus, @autoclosure 💡!

This code is also inherently delayed. This is an added benefit if the actual closure might end up being an expensive task or it might bring about some unintended side effects. The code provided is never executed until the closure it's wrapped in is.

Another quick one — where else might you have seen this in your recent iOS endeavors? How about assert()?

```swift
struct Programmer  
{  
    var isSenior:Bool  
    var appsShipped:Int  
}

let aSeniorDev = Programmer(isSenior: true, appsShipped: 13)  
assert(aSeniorDev.isSenior, "This dev isn't a senior!")
```
The first argument provided uses @autoclosure. If it weren't, again, the invocation might look something closer to this:
```swift
assert({   
    return aSeniorDev.isSenior   
}, {   
    return "This dev isn't a senior!"  
})
```
With @autoclosure, the code is simply more digestible, and I would also argue that it also makes for a far more enjoyable reading experience.

And, if you're curious how assert()'s signature looks, it's something like this:

```swift
func assert(_ condition: @autoclosure () -> Bool, _ message: @autoclosure () -> String = default, file: StaticString = #file, line: UInt = #line)
```
If you're thrown off by the other two parameters in the signature, we omitted them in the example because they have default values assigned to them. The more you know, right?

### Before You Go — Using Multiple Attributes

While each attribute has benefits that help it stand on their own, it's also helpful to pair them together in certain scenarios.

For example, take the @escaping attribute. The @escaping attribute signifies that the passed in closure can outlast the function it's passed to:

```swift
//A property on a view controller  
var onFakeCompletions:[()->()] = []

func fakeNetworkOp(_ completion:@escaping ()->())  
{  
    //Network stuff happens
    
    //The closure is appended to an external array outside of the   function's scope. This implies it could be invoked outside of the function - i.e., it could "escape" it  
    onFakeCompletions.append(completion)  
}
```
Considering this, we could use both @escaping _and_ @autoclosure for the same parameter.

Let's imagine H.R. let us know that any developer who is both a Senior in title and has shipped at least three apps is due for a raise, _but_ we also need to keep track of each evaluation for historical purposes:

```swift
class Programmer  
{  
    var previousPayRaiseEvaluations:[()->Bool] = []  
    var isSenior:Bool = false  
    var appsShipped:Int = 0
    
    func evaluatePayRaise(withAccolades raiseEvaluation:@escaping @autoclosure ()->Bool)  
    {  
        if raiseEvaluation()  
        {  
            //Give them a raise, and then save it to their records  
            previousPayRaiseEvaluations.append(raiseEvaluation)  
        }  
    }  
}

let aProgrammer = Programmer()  
aProgrammer.isSenior = true  
aProgrammer.appsShipped = 4

print("Past pay raise evaluations: (aProgrammer.previousPayRaiseEvaluations.count)") //0

aProgrammer.evaluatePayRaise(withAccolades: aProgrammer.isSenior && aProgrammer.appsShipped > 3)

print("Past pay raise evaluations: (aProgrammer.previousPayRaiseEvaluations.count)") //1
```
And just like that 💯!

There's certainly nothing prohibiting you from "chaining" attributes together, and when the situation calls for it, it works rather seamlessly.

### Wrapping Up

Attributes have always been something I've been particularly curious about. I like the idea of doing some heavy lifting in a clear, concise and simple way in my code — and that's really what attributes are getting you.

Of course, there are the popular ones from the bunch worth knowing about, such as the ubiquitous @objc attribute in Swift + Objective-C projects. Certainly an argument could be made that attributes are almost more akin to a necessity rather than a nicety.

But, lest we forget, Swift does exist outside of iOS projects. And that's an awesome thing, and my hope is that even though these attributes may not get the flashy headlines, perhaps they can help you when engineering your next bit of Swift code, iOS or otherwise.

Until next time ✌️.