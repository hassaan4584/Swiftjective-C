---
layout: post
tags: ["Series"]
title: "iOS Citizenship: Reacting to Low Power Mode"
author: Jordan Morgan
description: "It's a term you've likely heard in the development community - being a good citizen. What does it mean, and how can we do just that?"
image: /assets/images/logo.png
---
Ask not what iOS can do for you, but what you can do for iOS. That was in a keynote, right? No? Regardless, it's no secret that Cupertino & Co. put a premium on developers who spend the time to really shine up their software. Accessibility, multiple device support, energy efficiency — these are all important concepts that are often overlooked.

So, when and where do such concepts often show up? In award winning apps! The developers who always win those lovely Apple design awards have crafted code that works in tandem with their user interface to ultimately enhance the user experience. They are great examples of those who spend time on all of the details that contribute to being exemplary citizens on iOS.

This week, we examine one such detail. Our first step to becoming the fairest of all citizens on iOS is properly reacting to low power mode — introduced in iOS 9.

### Recognizing the Vampires

As we'll see shortly via some honest code samples — reacting to low power mode is trivial. The trick then becomes knowing what to do in such a situation.

To begin to form an understanding of things one might change in their software, one must look at what Apple does to iOS when lower power mode is activated. Such activities that are mutated in some form or fashion include, but are not limited to, the following:

* Reduced CPU and GPU performance
* Discretionary and background activities, including networking, are paused
* Reduced screen brightness
* Reduced timeout for auto-locking the device
* Mail fetch, motion effects, and animated wallpapers are disabled

Take a look at that rap sheet and ask yourself — which of those things happen in my own app? If they fit the bill, it's likely they are good candidates to either pause or terminate if low power mode kicks in.

Once you compile a list of processes to address, you can introduce them to the harbinger of their imminent destruction: NSProcessInfo.

### Meet the Delegator: NSProcessInfo

Apple brought out its big guns when it introduced low power mode with iOS 9. With his flawless hair glistening from the stage lights, Craig Federighi touted that the aforementioned mode would "turn switches and knobs you didn't even know existed" to extend battery life.

His claims are true, but we can certainly play our own part in the equation by way of our good friend NSProcessInfo. This thread safe singleton class can supply developers with all sorts of insightful information related to the current process by exposing unique process information agents.

Getting your mitts on such information typically only requires one invocation:
```swift
let machineName = NSProcessInfo.processInfo().hostName

//Prints out jordans-macbook-pro.local  
print(machineName)
```
### All the Information!

From our previous line of code, our new acquittance NSProcessInfo will attempt to interpret environment variables and command-line arguments into Unicode to return back as friendly UTF-8 strings. If the process can't be successfully morphed into unicode or the ensuing C String conversion fails — the process is ignored.

That said, this class contains a bevy of information. A close cousin to low power mode is the thermal state of a user's device. When the device reaches critical mass and is close to exploding, of course the first thing that comes to mind is pausing some network calls:
```swift
let state = NSProcessInfo.processInfo().thermalState

if (state == .Serious || state == .Critical)  
{  
    print("Evacuate ship — she's about to blow 💥!")  
    myBackgroundTask.suspend()  
}
```
Further, if one is wrestling with debugging the cause of a sudden termination — how about channeling your inner throwback sprit and busting out some Objective-C?
```swift
//A helpful little LLDB command to get the cause of termination  
p (long)[[NSClassFromString(@"NSProcessInfo") processInfo] _suddenTerminationDisablingCount]
```
`NSProcessInfo` has a lot to offer — it's an unsung hero that can help with a lot more than just low power mode. Speaking of…

### Identifying & Handling Low Power Mode

As the astute reader that you are, I'm sure you might've guessed that querying for the state of low power mode is quite elementary. And of course, you're absolutely right:
```swift
let lowPowerModeActivated = NSProcessInfo.processInfo().lowPowerModeEnabled

if (lowPowerModeActivated)  
{  
    //STAHP ALL THE THINGS  
}
```
That, however, would not be very opportunistic. It's obviously not realistic to query such a value in `viewDidLoad()`` all over our view controllers. Fortunately — Apple has bequeathed unto us some nifty notifications that can registered to so we can properly react to lower power mode changes.

For instance, lower power will be switched off by iOS should the device reach an eighty percent charge. Assigning a selector to be fired off for this event is the ideal approach:
```swift  
NSNotificationCenter.defaultCenter().addObserver(self, selector: "manageLowPower:", name: NSProcessInfoPowerStateDidChangeNotification, object: nil)

func manageLowPower:(note:NSNotification)  
{  
    if(NSProcessInfo.processInfo().isLowPowerModeEnabled)  
    {  
        //Reduce animations, lower frame rates, stop location   updates, disable syncs & backups, etc.  
    }  
    else  
    {  
        //Resume all of the above
    }  
}
```
### Wrapping Up

It's far too often understated, but we are craftsmen. As such, we seek to never leave a stone unturned — you've proven as much by virtue of just reading this post. Preparing your software for not just edge cases, but also responding to modern conveniences such as low power mode is a hallmark sign of an iOS craftsmen.

And — like any craftsmen worth his or her salt, they are undoubtedly good citizens of the iOS platform. So, go lock up an Apple design award due to your thoughtful implementation of responding to low power mode. Before you hoist the hardware, however, be sure to give me a wink.

Until next time ✌️.