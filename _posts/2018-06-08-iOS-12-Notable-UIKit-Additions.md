---
layout: post
tags: ["UIKit"]
title: "iOS 12: Notable UIKit Additions"
author: Jordan Morgan
description: "Our favorite framework for user interface creations becomes faster and more nimble with iOS 12."
image: /assets/images/logo.png
---
And here we are. We've finally got a good look at iOS 12 and all it has on offer. Though some chose to view it as a tame maintenance release, tagging it as such is a disservice and there was plenty to digest during the WWDC keynote.

Each year, I dive in on the latest version of iOS and try to bring up some of the new APIs that our favorite framework, UIKit, has brought to the table. In no particular order, let's dig in on some of the enhancements that caught my eye.

### UITextInputTraits

Apple continues its push towards keeping its user's data private and secure, so it's no surprise to see Cupertino & Friends© extend the password autofill APIs.

New this year is the ability to suggest a new password for your users, _and_ supply your password parameters dictated by business requirements to iOS when suggesting such a password. This is done via the `UITextInputPasswordRules` class:
    
```swift
let createNewPasswordTextField = UITextField()

let newPasswordReqs = UITextInputPasswordRules(descriptor: "required: lower; required: digit; max-consecutive: 3; minlength: 12;")

createNewPasswordTextField.passwordRules = newPasswordReqs

// Now, when iOS suggests a new password - these rules will be used to generate it
```
The pertinent information here is the descriptor parameter, which is a plain string that follows a certain syntax, à la the visual format language:
    
    
    "key: value;"

Think of it a dictionary entry that always is followed by semicolon. It's quite close to CSS declarations. With it, you can specify the following items:

* required : Self explanatory
* allowed : Allow a subset of allowed characters
* max-consecutive : Restrict the number of successive characters

And character classes to match against those rules:

* `upper` : A-Z
* `lower` : a-z
* `special: `-~!@#$%^&*_+=`|(){}[:;"'<>,.? ] and space
* `ascii-printable` : All ACII printable
* `unicode` : All unicode

To further things a little, suppose you only wanted to allow the letters "j,o,r,d,a,n" because you want the strongest password that I'll never, ever most definitely guess, then you could do this:
    
    
    UITextInputPasswordRules(descriptor: "required: [j,o,r,d,a,n]; max-consecutive: 2; minlength: 12;")

Be aware that the framework has some validation against your supplied validation, resulting in some form of validationception.

Your parameters have to _at least_ use two instances of the ASCII uppercase letters, digits and ASDII lowercase letters classes. Other than that, it also must be longer than 12 characters.

If you don't meet this criteria, do you get some sort of runtime error or exception? Nope — the user agent just throws out your insecure, primitive suggestion and uses the default. Which is probably a good thing.

As a bonus, you can do the same thing in HTML by using the `passwordrules` attribute in your input element.

### One Time TFA Codes

In one of my favorite "It just works" APIs that Apple supplies to developers, it's hard to argue that there's something more trivial to implement in iOS development that simultaneously brings real value to users than setting a text content type.

The powerful heuristics of iOS sucks in passwords and phone numbers, can suggest a relevant address and more. And now, that more is TFA codes, accomplished by doing nothing more than choosing `oneTimeCode`:
    
    
    aTFAtextField.textContentType = .oneTimeCode

This also joins the new value, `newPassword`, which would enable the password creation prompts touched on above. The usual restrictions (if you can even call them that) is that the element accepting the password must be a text field, text view or a view that adopts the `UITextInput` protocol.

Of note, tvOS apps are also granted the same affordances when using the control center keyboard, the continuity keyboard or even Cupertino's Remote App. And let's face it, nobody wants to type on that platform so any shortcuts we can provide just promotes engagement that much more.

Text content type is powerful, but also the definition of lightweight, simple and WYSIWYG. Sometimes what you aren't is just as important as what you are. That's also true with framework design.

### Graphics Rendering

As we'll briefly discuss later on, iOS 12 has automatic backing store support for views. The depth of their content drives this. So, for example, if you are rendering a grey scale image on the screen iOS will employ an 8 bit per pixel backing store instead of the usual 64 bit per pixel backing store a portrait image would incur.

The cost savings is significant, in the [What's New in Cocoa Touch][1] session, Apple engineer Josh Shaffer notes that the previous example goes from 2.2 megabytes of real estate down to _275 kilobytes_.

As aforementioned, views get this out of the box. If you draw into offscreen bitmaps using `UIGraphicsImageRenderer`, though, iOS won't be able to predict the developer's intentions with the resulting image. As such, a configurable buffer backing store style has been introduced so one can take part in the memory savings:
    
    
    let rendererFormat = UIGraphicsImageRendererFormat.default()  
    rendererFormat.preferredRange = .extended // For an extended range image
    
    
    let renderer = UIGraphicsImageRenderer(size: CGSize(width: 100, height: 100), format: rendererFormat)

Above, we indicated our intention to utilize an extended range image. Though, we can also indicate that its unspecified, automatic or standard.

### The Small Quick Win

Detecting user interface orientations is traditionally frowned upon via Apple's official stance. And though trait collections offer us most of what we need, it's still refreshing to see Apple come full circle on all the edge cases with two new additions here:
    
    
    let device = UIDevice.current  
    let isFlat = device.orientation.isFlat  
    let isValid = device.orientation.isValidInterfaceOrientation

### Darkness for Days

Also, we have dark mode on iOS, finally! Mojave doesn't get all the fun!

Err…shoot, no wait — we just have API support for it. But it doesn't officially exist. But it also kinda does too, because the code is there. It's shipped with Xcode.

I don't know. I'm just telling you that trait collections now know about it:
    
    
    let darkTraitCollection = UITraitCollection(userInterfaceStyle: .dark)

…there's obviously enumerations for it:
    
    
    @available(iOS 12.0, *) public enum UIUserInterfaceStyle : Int {  
    case unspecified  
    case light  
    case dark  
    }

…but they only apply to CarPlay on iOS 12 beta 1.

So there you go 🤷🏻‍♂️.

### Notifications

Technically not part of UIKit, but I did have to highlight one welcome change aside from the new grouping capabilities. Look, dealing with notifications is often a pain from a developer perspective. While not a forgone conclusion, the more notification offerings we have to support, generally the issues that could arise grow exponentially.

You have the system notification view to toggle app permissions within iOS' settings, possibly your own user interface to allow for granular choices, APNS to go through and oh, let's not forget the network to contend with too.

So, the fact that you can deep link directly into your app's notification settings from an incoming one is not a small improvement (via `providesAppNotificationSettings`), but a very welcome change as developers continue their journey towards simplifying notification issues for them, the end user and customer support.

🕺!

### … And The Free Ones 🙌

[iOS 12 is fast][2]. A lot of the improvements we'll enjoy come from deep within the framework itself. Here, I've chosen to highlight API changes you'll need to put some time into to reap benefits.

But that's the thing — our apps will feel a bit smoother, faster and coherent without us having done anything at all.

The reasons why range from smarter cell prefetching via the API scheduling things serially, smarter CPU diversification, more intelligent backing stores for `UIView` and Auto Layout (quite impressively) hitting O(n) instead of O(n²) for multiple common layout scenarios, to name a few.

### Wrapping Up

Personally, I came away more impressed than I thought I would be with iOS 12. Initially, it appeared that a lot of the chatter preemptively declared that iOS 12 would be a day late and dollar short. Last year, we were treated to some marquee features within UIKit like drag and drop — so what could they throw down for us this year?

But, as is typical, W.W.D.C. brought some new stuff we weren't expecting, hardening updates and most importantly the new APIs. Exciting times, plus — our apps are just better by virtue of simply running on the new OS. UIKit will always be at the forefront, and this year was no exception as there's still plenty of discussions left to be had around this year's improvements.

Saddle up 📱

> Missed iOS 11's notable additions from last WWDC? Got you covered [here][3].

[1]: https://developer.apple.com/videos/play/wwdc2018/202/
[2]: https://twitter.com/_inside/status/1003831980025372673
[3]: https://medium.com/the-traveled-ios-developers-guide/ios-11-notable-uikit-additions-92e5eb421c3b

