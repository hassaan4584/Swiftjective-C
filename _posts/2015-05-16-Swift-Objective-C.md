---
layout: post
tags: ["Swift"]
title: "Objective-C and Swift Together"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "We've now hit the inflection point where your Objective-C projects must mix with Swift, or vice-versa. Here's how my first experience with it went."
image: /assets/images/logo.png
---
It's no question — swift is the new toy and marked as the future. Objective-C is showing its age and will, eventually, be much less prevalent. But in the here and the now, the two must learn to coexist peacefully.

And that's ironic, because the two couldn't be further apart in many respects. One is built and tuned for iOS development from birth. The other was tacked on to an aging programming language and repurposed for modern technology.

This week, we'll look at some interoperability observations I've had with the two languages since mixing them frequently. Most recently — integrating swift into production code laced with four years worth of Objective-c.

### Bridging The Gap

Swift interop generally falls within one of two scenarios:

* The programmer wants to slowly introduce swift into Objective-C
* The programmer needs to use Objective-C with a new swift project

And neither approach is wrong. But accessing code between the two is fundamentally different.

### Meet {project-name}-Bridging-Header.h

This useful file is what your project will use to, very literally, bridge the gap between Objective-C over to swift. In practice, it works much like a prefix header file. One simply includes the Objective-C header file(s) they wish to expose to swift.

To generate such a file, adding any Objective-C to a swift project or vice-versa will produce a bridging header prompt from Xcode.

This prompt should greet you when beginning mix and match programming in Cocoa Touch.

However, Xcode has always played by its own rules. In my experience, this prompt displayed only two out of three times. Don't fret, they are trivial to add manually.

To do so, simply add a header file and specify it as the project's bridging header under the project's build settings.

### Also, meet {project-name}-swift.h

This header file is also created automatically, and its reason for existence is to expose swift code to Objective-C.

This beautiful arrangement is due to swift's ability to be accessible to other swift files contained within the same module. For this reason, there is no need to create some sort of bridging header with several swift file imports in it to expose to Objective-C. This is also why it's unnecessary to import swift files to other swift files — the opposite of what Objective-C requires.

This should be met with care, however. Recall that Objective-C is lacking a multitude of swift's capabilities such as tuples, generics, and so on. Code utilizing these techniques will either go unnoticed by Objective-C or provide one with a runtime exception, on the house.

In case you wanted specifics, here is the naughty list for swift to Objective-C interop:

* Generics
* Tuples
* Enumerations defined in Swift
* Structures defined in Swift
* Top-level functions defined in Swift
* Global variables defined in Swift
* Typealiases defined in Swift
* Swift-style variadics (but Objective-C has its own implementation)
* Nested types
* Curried functions

### Gotcha'

Which leads me to a subtle, but pesky little gotcha. If one tries to access a swift specific property that doesn't support Objective-C, it likely won't show up in any form. Like a thief in the night, the property seems to have simply vanished and left us for the greener pastures of CMD + Z.

This would be obvious to most if the property was a more prominent swift feature, such as the tuple, but much less so with something like this:
```swift
var anID:Int?
```
Any guesses as to what this will show up as in Objective-C? Spoiler alert:

Nothing is the correct answer. And rightfully so, swift's optional value types are not bridged over to Objective-C. Objective-C only allows for reference types to be nil, certainly not its primitives.

Thus, autocomplete just carries on with its merry day and the programmer stays none the wiser.

### @objc

This little bugger seems to be the most widely misunderstand attribute in swift. When blindly troubleshooting interop between the languages, this attribute is commonly placed before swift class declarations.

And sometimes, that's correct. And other times, it's incredibly redundant.

#### New To Old

The problem begins like this. There is a new swift class that is needed in Objective-C. For whatever reason, it's not working.
```swift
//In swift  
class Foo:NSObject  
{ 
}

//Later in Objective-C  
#import app-swift.h

//Compiler warning of some kind appears here  
Foo *aFoo = [Foo alloc] init];
```
And so it is, the programmer hastily annotates it with @objc:
```swift
//In swift  
@objc class Foo:NSObject  
{
}
```
But after a clean and build, the heavens sing, Steve Jobs smiles upon you in a sly manner from above, and the project compiles. Issue being — the project likely compiled because of the clean, which let the build run through that could then update the project-swift.h header. That, in turn, made the class visible to the Objective-C implementation file.

Point being — it's not because of the added @objc attribute. **The only time you need to explicitly add this attribute is if your swift class doesn't inherit from an Objective-C object directly.**

From our previous example, if Foo did not inherit NSObject, then the @objc attribute would be completely warranted. Since it did, however, the compiler just adds it for us and we both move on with life.

### Inheritance

Due to our discussion a few hundred characters ago over Objective-C lacking some of swift's toys, it only stands to reason that inheritance can only be executed down a one way street.

That is, swift can certainly do (most) of what Objective-C can, but certainly not the other way around.

Fancy a real wold example? Please pardon the contrived names:
```swift
class SwiftBaseViewController:UIViewController  
{  
    //common view controller code  
}
```
Say you had added this to an existing Objective-C project, which already had something similar:
```swift
@interface ObjCBaseViewController:UIViewController
```
This means it would be legal to inherit this object in a swift class:
```swift
class NewViewController:ObjCBaseViewController
```
But this would not compile:
```swift
@interface NewViewController:SwiftBaseViewController
```
…because who knows what's hanging out in your swift code that could tear the proverbial innards from your Objective-C class.

### Housekeeping

I encountered many other obstacles along the very unbeaten path of interop, but none that couldn't be slain in a timely manner via a trip to the documentation. That said, maybe these final tips will save you that trip:

#### **Public or Internal?**

The public modifier is only necessary when exposing swift to another framework. Otherwise, the default internal modifier will be jovially picked up in the project-swift.h header for code in the same app target.

#### …or Private?

Use private as one normally would with Object Oriented Programming practices. The only time they will appear in Objective-C from swift is if they are manifested as IBActions or IBOutlets.

#### Bridging Header Naming Conventions

Use your product's module name as opposed to the target name when naming the bridging headers. Keep this is mind when adding it manually if Xcode deemed you unworthy of creating it automatically.

#### Objective-C's Bridging Header Path

This path should relative to your project and point directly to where the file is located, not just the directory that it's contained in.

#### Recap on swift in Objective-C

If swift is to be used in Objective-C, it must either inherit from an Objective-C class or use the @objc attribute.

#### Frameworks

Set Defines Module should be set to YES under the Packaging section in Build Settings if one is working with frameworks.

#### Old Habits Die Hard

And just for fun, count how many times you prepend "@" in front of your swift string literals without even flinching.

### Wrapping Up

If you are like me, you've experienced many a loving year with Objective-C. It's sad to see her go, which I believe she will eventually. Though it's many years away, just ask the hard working students who submitted apps for WWDC scholarships...who were required to use Swift.

Swift was a requirement to be considered for a WWDC scholarship.

Be that as it may, it's a classy move by the powers that be at Apple to provide such stable workflows for mixing the two languages. Though in reality, it's a move out of necessity rather than novelty, we can enjoy it for what it is: the best of (let word = "both", @"worlds").

Until next time ✌️.
