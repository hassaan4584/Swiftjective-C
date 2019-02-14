---
layout: post
tags: ["Swift"]
title: "Access Control"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Abstraction is a tent pole component of modern programming. Swift's robust tooling for access control can help us enforce it."
image: /assets/images/logo.png
---
I still remember it like it was yesterday, as the saying goes. I was a young, newly minted programmer in college — and I was building out some basic Windows Forms apps to hone my craft.

And I tell you what — my novice mind couldn't be bothered with things like encapsulation. I was all about that sharing, public access for all the things! In perhaps more relatable terms, with iOS — developers the world over stuff all sorts of goodies into the app delegate. We've all done it.

So today, let's talk about how Swift can help us keep things tidy via a brief flyover of its access control features.

### K.I.S.S.

Swift adheres to the KISS methodology when it comes to access control, and in my opinion it's better for it. One has three options at their disposal.

To break the ice — let's first meet the good ol' **public** option— which bows down to no man preaching the importance of privatization. Entities marked as public can be used by any consumer defined within its module — or from another source file in another module that imports its containing one:
```swift
public class AwesomeControl : UIResponder  
{

}
```
Think creating an interface for the next amazing framework for Swift when contemplating the use of public.

The next option is slightly more restrictive, and popular — and that's the **internal** option. Here, entities within the same source file inside of their defining module can access its beautiful Swift contents.

However, if there is a piece of Swift wanting to get in on the action _outside_ of its module — no can do. We've gotta go public for that kind of action. This, however, is the default level of access control that Swift gifts unto you. For the most part, it makes sense to use.

This default assignment holds true even if you've made the entity public. Its members will still be treated as internal by default — which promotes Swift's stance that one has to be incredibly intentional when making things public.
```swift
public class AwesomeControl : UIResponder  
{  
    internal volumeLevel:Float  
    trackLength:Float //Still has internal access by default  
}
```
This helps ensure that the public-facing API for a type is something you've consciously opted in to. Personally, I think that helps one avoid accidentally exposing a part of the API by happenstance.

Think internal when building out framework or app structure.

Last, though certainly important — we've the introverted **private** option. Here, we're restricting any use of the entity outside of its defining source file.
```swift
public class AwesomeControl : UIResponder  
{  
    internal volumeLevel:Float  
    trackLength:Float //Has internal access by default
    
    //MARK: Private Properties  
    private trackHistory:Array  
}
```
Think private when one needs to hide the implementation details of a specific piece of functionality. We're alluding, of course, to that whole encapsulation thing.

### When to Assign What

If you're new to access control, it may a bit daunting to know when to assign a specific level of accessibility to an entity. The thought process can be relatively demystified by following Swift's manifesto for such things:

> _"_No entity can be defined in terms of another entity that has a lower (more restrictive) access level_"_

For example, a public variable couldn't be defined as having an internal type. And, that makes sense because the type might not be available everywhere that the public variable is put to use inside of the module.

The recurring theme here is the notion of accessibility in terms of modules and source files. We know what source files are, and since access control works around those, it makes things like this possible:
```swift
private struct SecretThings  
{

}

public struct NonSecretThings  
{  
    //SecretThings is available due to being in the same source file  
    private let secretIsOut = SecretThings()  
}
```

Access levels in source files are pretty much industry standard.

### What's a Module Then?

Since access control works a bit differently in Swift than other popular languages, a bit of bookin' up on Swift's parlance can really help one to understand things. I think the Module is an important place to start.

A module in Swift represents what most of us call a framework. It's an application that's built and shipped as a singular unit. It's a unifying chunk of distributed code. They are sucked into other modules and source files via the _import_ keyword.

For instance, open up a Playground file, erase everything and write this:
```swift
let aView = UIView()
```
It errs, and that's because the Playground file has no idea what UIView is. But, importing UIKit's module solves things, and opens up UIView for use:
```swift
import UIKit  
let aView = UIView()
```
In terms of Xcode, each and every build target is represented as its own module in Swift. So, say one has some amazing HTTP networking code they'd like to use across another app. When one creates that as its own stand alone-framework — then it will be a separate module when it's imported and put to use in another app (or framework).

It's certainly possible to get granular here as well. Swift makes it easy to show intent on module imports:
```swift
import class UIKit.UIView  
let aView = UIView()
```
If needed, this affordance can be extended to structs, protocols and even functions:
```swift
import struct AModule.GreatStruct  
import protocol AModule.SomeProtocol  
import func AModule.aFunc
```

### Talk Specifics

There are some waters you may find yourself in where certain access control rules may vary. Take the friendly [tuple][1], whose [pattern matching prowess][2] and usefulness extend far into the realms of everyday Swifting.

Except for tuple splatting — [RIP][3].

Things like tuple types aren't benefitting from a standalone definition like structs or classes do. Because of that — their access control is automagically inferred when it's actually used as opposed to being specified by the developer. True to Swift's rules — the level will be selected that is most restrictive:
```swift
private struct SecretThings  
{

}

public struct NonSecretThings  
{

}

//Private because SecretThings is more restrictive than NonSecretThings  
print("Implicitly private tuple: (SecretThings(), NonSecretThings()))")
```
In much the same way, access levels for functions are deduced by the most restrictive access level of the its return and parameter types.

Other things that you may not associate with every day access control, like the enumeration — are relatively simple to make sense of:
```swift
enum JordansEveningPlanProspects  
{  
    case DoLaundry  
    case CookDinner  
    case MowLawn   
}
```
Here, I couldn't make DoLaundry private, since the enum's member access control are taken straight from the enum's declaration, which is internal in this case.

### What's Access Controllable

At first I thought it would be best to write a little bit about this topic, but nay — I feel a good ol' bullet list coming on since it feels more effective:

* Classes
* Structures
* Enums
* Properties
* Methods
* Initializers
* Subscripts (belonging to these previous types)
* Protocols
* … even global constants, which feels like an oxymoron.

### Wrapping Up

Access control in Swift works a bit differently — but in a no-nonsense way. I'm down with that. The less I have to think about, the better and more stable code I typically write. Once the concept of modules and Swift's rules on its access modifiers is uncovered — the whole process will unfold smoothly in your own code.

Until next time ✌️.

[1]: {{ site.url | append:"/swift-tuples" }}
[2]: {{ site.url | append:"/tuples-pattern-matching" }}
[3]: https://github.com/apple/swift-evolution/blob/master/proposals/0029-remove-implicit-tuple-splat.md
