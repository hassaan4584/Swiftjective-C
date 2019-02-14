---
layout: post
tags: ["Series"]
title: "iOS Architecture Patterns: Part 2"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Our second discussion on iOS architecture patterns. Here's how delegation is imprinted across Cocoa Touch."
image: /assets/images/logo.png
---
If we're being honest iOS developers, it's pretty much gospel that the majority of iOS apps are a list of things. Or, if you're up with the times, a collection of things.

And, when we get bit by the iOS bug (an ailment for which there is no cure) — our first _Hello World_ exercise is the to-do list app. This undoubtedly leads us to our bread and butter — the table view. And where there is a table view, there is also a common pattern at play.

Today, we'll introduce one of the most pleasant design patterns that literally invades UIKit — the delegate pattern.

### Such Control

For the stubborn lot of us in the crowd, the delegate pattern will be second nature. It's all about delegating work off to someone else — or at least, letting them know when to try and do something.

The beauty in the whole process is that, [like the last time we talked about patterns][1], you've likely been doing this since you first booted up Xcode. Whether you've fully understood it or not, it's a fluid pattern to follow and implement.

The fun, however, comes when one grasps how to use it, and when.

### The Setup

If one is familiar with the decorator patter, it's clear to see that delegation is a close cousin. It borrows a lot of similar ideas, but when you think of delegation, just remember this: it's simply a design that allows another object do something on another object's behalf. It's a purposeful coupling and coordination between the two. A marriage of sorts, if you will.

Now, imagine you are one of the fine engineers working on UIKit in the super secret underground labs in Cupertino. Mr.Ive swipes his security clearance card to access said lab, and after a thorough decontamination process — he slowly approaches your desk.

When he arrives, you slowly look up from behind your mac. He says to you, in the most elegant accent the world over — "I need iOS to have a ton of lists. Make it so." Likely, he would word this a lot more elegantly, but alas — you are tasked with coding up the famous UITableView.

### Nuts and Bolts

Now, think on this for a moment. Specifically — how would we handle a user tapping a row inside the table view? Does every object need to inherit from a table view and override a function? That's quite a bit of overhead, plus it doesn't play well with Objective-C. So — inheritance is out.

Next up, maybe we could have a closure or block as a property that is assigned to every time we want something to happen. That's going to work in theory, but it's likely to be a mess in practice. Plus, we've determined a rather strict route for the architecture, and it's going to require a good bit of refactoring to change up.

In this case, we've recognized that flexibility is paramount. A lot of iOS developers are going to need to use this control. And in a thousand and one different ways. It's clear now that we know _what_ we want a table view to do, but _how_ to do it should be up to the object using it.

Thus, you add this small, little property on a table view — and iOS was never the same again:
```swift
@property (nonatomic, weak, nullable) id  delegate;
```
The delegate property is the first piece to this software architecture puzzle. With it, we can assign another object to the things we want to do, but in a very abstract way. To connect the dots — think of how many times you've either seen or authored something similar to this:
```swift 
class aVC:UIViewController, UITableViewDelegate  
{  
    //MARK: Table view delegate  
    func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath)  
    {  
        print("Tapped a row")  
    }  
}

let tvDelegate = aVC()  
let tv = UITableView()

// The magic  
tv.delegate = tvDelegate
```
At this point, the table view object knows that it has an object that will do something when when certain messages are sent. Yet — it knows nothing about the specific implementation — as well it shouldn't.

### The Takeover

Keeping with our code sample above, the aptly named instance variable _tvDelegate_ has specified to the compiler that it will act as a delegate to a table view. Of importance to note, clang will also gripe and yell if we haven't implemented all of the required delegate methods.

With a table view delegate, all the method signatures are marked as optional — so we're in the clear here:
```swift
@protocol UITableViewDelegate  
@optional  
//Delegate method signatures  
@end
```
That said - this is the correct place to specify intent, if need be. If a delegate of the protocol you've authored won't do much without a certain method, then mark it as required. Let clang be your bouncer.

Regardless, the state of our little code snip has done something amazing. When a table row is tapped, we will print out to the console. That's perfect because the table view isn't responsible for what happens when a row is tapped — it's delegate is.

Behind the scenes, the object (here, a table view) who has a delegate (the view controller) simply checks to see if it's interested in doing something when a row is tapped. If so — a message is sent to the delegate for the method in question:
```swift
//Close to Apple's implementation  
if (_delegateHas.didSelectRowAtIndexPath)   
{   
    [self.delegate tableView:self didSelectRowAtIndexPath:rowToSelect];   
}
```
And, to be honest, that's really all there is to it.

### Ah, Lightbulb

Once one feels comfortable with such a pattern — there's no better way to master it than to roll up your sleeves and code it up yourself. A neat little trick that was made popular by [Objc.io][2] is to separate out a given delegate.

And why not? A delegate is nothing more than an object, so who is to say we can't just specialize one if we want all of that boiler plate out of your view controller? It's easily done, and I highly recommend reading the linked article above as it also sheds a lot of light on how delegation looks in the alleged "real world" of iOS code.

[I actually use this one all the time][3], as nothing brings a brighter smile to my face than view controllers around ~450 lines in my own projects.

### Easy Now

Though your friendly delegate pattern is a breeze to plug into any code base, be thoughtful not to over do it. Recall that we asked ourselves a few questions before we started coding our hypothetical table view that Mr.Ive requested — and it led us to delegation.

This applies to any pattern, I suppose, but before you go down the proverbial rabbit hole, try and poke holes in the decision. It sometimes pays to be the devil's advocate when making choices on how to craft your software.

### Wrapping Up

Learn only as much as you need to ship it. That's been my mantra when learning new things. In a world where there are more outlets than ever in human history to learn new things — I've found that the trick is knowing when to put a hiatus on my learning.

And it served me well with iOS.

I may not have fully understood the inner workings, but years ago I knew how to get a table view up and running. Looking back, had I known how easy these patterns were to grasp in the beginning — I would've spent more time on them too.

Until next time ✌️.

[1]: {{ site.url | append:"/architecture-patterns-in-ios-part-1"}}
[2]: https://www.objc.io/issues/1-view-controllers/lighter-view-controllers/
[3]: https://github.com/DreamingInBinary/GenericDatasource