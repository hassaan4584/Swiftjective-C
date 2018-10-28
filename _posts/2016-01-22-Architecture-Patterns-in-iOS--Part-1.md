---
layout: post
tags: ["Series"]
title: "iOS Architecture Patterns: Part 1"
author: Jordan Morgan
description: "Our first discussion over iOS architecture patterns. Let's peer into the world of target-action."
image: /assets/images/logo.png
---
Throughout my short lived career as a developer, I've observed that most of us tend to fall into one of two categories. We either hack away and get things done as [quickly as possible to deliver an MVP][2] — or we constantly speculate over every detail, in turn crafting the finest and most scalable architecture the world over.

And , who knows which is right? Personally — I think a healthy mix of the two might be the sweet spot.

In the name of taking the latter approach, though, we will start covering some architecture patterns in iOS that you've likely been using for years and perhaps were unaware of. First up — **the target-action pattern**.

### In My Sights

In essence, the target action pattern allows an object to deliver some information about an event to another object. That's it. You might say it's all in the name, and you would be correct.

So, why mention it? Well, Cocoa Touch has a mad crush on it. So as iOS craftsmen, it'll only help us in our endeavors if we are not only aware of it, but also have a fundamental understanding of it to boot.

And why not? It's simple, effective, and it works.

### So — How Does It Work?

The target-action pattern (henceforth known as TAP for this post) is architected much like Apple's hardware is designed — extremely simple and purposeful. It only has two key components:

* The Information payload
* The receiver

With only these two things, an impressive amount of Cocoa Touch is stitched together. For the developer who enjoys constructing their UI in code, TAP is a standard and likely daily affair.

This is still true even when one considers the rise of Swift. Objective-C places a lot of bets on message sending, and things such as TAP fit like a proverbial glove in such a world.

Even so, Swift doesn't employ TAP on a language level basis — remember, this is a Cocoa Touch concept. It's a great reminder that both Objective-C and Swift are but only two tools that help create iOS apps with a lot of aid from Cocoa Touch.

### Let's Get Practical, Practical

Close your eyes, imagine yourself in a peaceful, beautiful user interface. There is gorgeous, spacious and breathable negative space all around. You immediately realize you are not in Apple Music (sorry, had to). To your left you see an approachable UIButton just clamoring for your touch. You oblige, yearning for the _result_ of your _action_ to become clear unto you:
```swift 
let aBtn = UIButton(type: .Custom)  
aBtn.setTitle("Hey-Oh!", forState: .Normal)  
aBtn.addTarget(self, action: "logIt:", forControlEvents: .TouchUpInside)

func logIt(sender:UIButton)  
{  
    print(sender.titleLabel?.text)  
}
```
TAP made made the magic happen in the above scenario. Let's deconstruct it a bit.

### Component 1: The Information

Since we know that TAP sends information somewhere, and then something acts on it, it seems like taking a peek at how the information gets set and sent would be a logical place to start.

In the code sample, assume that it was authored inside a typical view controller. Given this, it's straightforward to identify the two objects in play:

* The view controller, who will act as the target
* And the UIButton, who will act as the sender that carries and constructs the information payload

When the button is tapped, it kicks off what's officially called the _action message_. The information inside the message is also incredibly small and focused. The perceptive reader will start noticing a pattern — TAP is unapologetically simple.

But hold up — why did it send the action message when it was tapped? That's because in the method signature we also defined the event to kick things off. As is common with UIButton — that event here was TouchUpInside.

The information that's carried in this instance is two-fold: the target and the action (surprise surprise). This information allows Cocoa Touch to know who's sending the message, who will handle it and with what action:

* **Who sent the message:** A UIButton
* **Who's handling it:** The view controller
* **What's the action:** logIt:

### Component 2: The Receiver

At this point, the view controller has received the action message. The action message was able to get there by traveling the responder chain.

Conversely, if the target parameter was set to nil, Cocoa Touch would've kept traveling up the responder chain until it found an object who could respond to logIt: — a useful (and sometimes error prone) technique within iOS programming.

Now, the view controller will invoke it's specified action contained in the action message. Going further, it's often helpful for developers to query the sender to get more context about the event responsible for kicking off the action message.

### OS X != iOS

I've purposely been mentioning TAP in the context of Cocoa Touch. While TAP exists between OS X and iOS, they don't quite work the same. In theory, they are quite similar— while in practice they get it done a tad differently.

The short story is this: Cocoa Touch allows for TAP to be mapped against several different signatures for action methods. It's important to be cognizant of mobile interaction, which commonly consists of multitouch events. Because of that, a control can map a target and action to several events that can occur on the control (swipe up, down, left, right, etc.).

### Control-Cell Variant

If we switch back to the lavish landscape of 27 inch retina displays and OS X, we can quickly begin to deduce that TAP's use case is different. Within AppKit, TAP implements the control-cell pattern for most cases. I hate to go all inception on you, but it's really just a sub pattern of an existing pattern.

Regardless, in this scenario a control can own one or multiple lightweight cells. When the event is bubbled up, the information that was needed in the TAP world comes from the cell itself. This would mean that within the rules of TAP — the cell would be responsible for sending the action message to the target.

**TL;DR:** Click on control x → control x gets the information from its cell → TAP takes over

### TAP + iOS

With all this talk about design patterns, it's also helpful to ice things off with some good, old fashioned context. We've been talking about hooking things up using TAP by creating a UIButton programmatically. While it's true that the object sending the message could really be anything, TAP is famously used for controls.

Their signatures follow the same pattern, while also allowing for flexibility. If you don't need to use multiple controls for the same action, I personally prefer to strongly type the sender. Here are a few examples that TAP would work with:
```swift 
//Programmatically  
func doIt()
func doIt:(sender:AnyObject)

//or with .xibs or storyboard  
@IBAction func doIt:(sender:AnyObject) 
@IBAction func doIt:(sender:UIButton)
```
### Wrapping Up

Early on in my development career, I didn't give much thought to programming patterns or architecture in general. I thought it was complex ideas and formulas better left to those who have to scale software at Google's size.

It turns out, they aren't scary. They aren't even complicated. They are just bite sized ways of thinking about developing software. The good news is, iOS and Cocoa Touch are rife with many interesting patterns and concepts, TAP being among one of the most simple (and helpful) of them.

Until next time ✌️.

[2]: https://open.buffer.com/healthy-naivety/
