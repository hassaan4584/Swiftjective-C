---
layout: post
tags: ["UIKit"]
title: "UILayoutGuide"
author: Jordan Morgan
description: "Spacer views often make user interface creation more palatable, but with significant drawbacks. There's a more pragmatic way."
image: /assets/images/logo.png
special: "true"
---
It's astounding to think that we lived in a pre Auto Layout world not that long ago. Auto resizing masks and `CGRectMake()` ruled the lands of user interface development for quite some time.

But as Apple's devices started stacking up various point sizes, it was obvious that developers would either languish in the pit of misery that can result from too many frame calculations or embrace the power of describing relationships. The latter, obviously, won out.

And thus, Auto Layout has been [used][1], [DSL][2]'d and [criticized][3] ever since. With its rise, though, another layout paradigm also came into prominence. The "spacer" view. Or dummy view. Container view.

Whatever you call it, we've all used them. But Apple, as so it often does, says there is a better way. This week, let's chat `UILayoutGuide`.

### But Dummy Views Rock (…and I agree)

Dummy views solve some layout problems extremely well. That's why we all use(d) them. Expressing inter-view relationships, creating modular chunks of your user interface or defining constraints to express the coordinates or size of empty spaces between views all called for the dummy view.

In fact, even if one simply wished to center a group of controls in a particular coordinate space, a dummy view was often used to contain them.

As such, a lot of important jobs were all entrusted to a construct that was never meant to truly do any of them. If you contest that notion, ask yourself what a view actually does in an iOS application.

Or better yet, let the docs tell the story:

> A view object renders content within its bounds rectangle and handles any interactions with that content.

> A view is a subclass of `UIResponder` and can respond to touches and other types of events.

So it's hardly disputible to reason that an important part of a view being a view is to render stuff and handle events. Dummy views, views though they are — shouldn't particpate in any of those activities at best and _do_ participate in some of them at worst.

Using dummy views, we've

* Incurred the cost of a view that's only helping to define a layout.
* Added a first class member of the view hierarchy, joining in on all the overhead that may be associated with any task related to it.
* And as part of the responder chain, it could intercept some messages that it was never intended to handle.

😬.

### There, but Not Really

But a layout guide is none of those things, nor does it suffer from any of those problems. It's a non-rendering view, much like its more powerful cousin, [`UIStackView`][4].

Unlike a bonafide view, a layout guide actually doesn't define a view. Instead, it just represents a retangular region in their owning view's coordinate system. That's it. This is what allows it to interact with Auto Layout.

The API closely, and purposely, mirrors that of a view:
```swift 
let scrollView = UIScrollView()

// Instead of this...  
let containerView = UIView()  
scrollView.addSubview(containerView)

containerView.widthAnchor.constraint(equalTo: scrollView.widthAnchor).isActive = true  
containerView.heightAnchor.constraint(equalTo: scrollView.heightAnchor).isActive = true  
containerView.leftAnchor.constraint(equalTo: scrollView.leftAnchor).isActive = true  
containerView.rightAnchor.constraint(equalTo: scrollView.rightAnchor).isActive = true

// We can do this...  
let containerLayoutGuide = UILayoutGuide()  
scrollView.addLayoutGuide(containerLayoutGuide)

containerLayoutGuide.widthAnchor.constraint(equalTo: scrollView.widthAnchor).isActive = true  
containerLayoutGuide.heightAnchor.constraint(equalTo: scrollView.heightAnchor).isActive = true  
containerLayoutGuide.leftAnchor.constraint(equalTo: scrollView.leftAnchor).isActive = true  
containerLayoutGuide.rightAnchor.constraint(equalTo: scrollView.rightAnchor).isActive = true
```
### API Particulars

The `UILayoutGuide` class is designed to perform all the tasks previously performed by dummy views, but to do it in a safer, more efficient manner.

Layout guides do not define a new view. They do not participate in the view hierarchy. Instead, they simply define a rectangular region in their owning view's coordinate system that can interact with Auto Layout.

The flow, shown above, is straightforward:

* Instantiate a layout guide.
* Invoke `addLayoutGuide(_:)`to the desired view.
* Set up valid constraints on the layout guide.

Let's imagine you needed to constrain some views and center them, but only in the bottom left corner of a view. As in, if you cut the view into four corners, we want to add stuff to the bottom left one. Layout guide makes this easy as it was with dummy views without the baggage:
```swift
let bottomLeftGuide = UILayoutGuide()  
view.addLayoutGuide(bottomLeftGuide)

// External Constraints  
bottomLeftGuide.leftAnchor.constraint(equalTo: view.leftAnchor).isActive = true  
bottomLeftGuide.bottomAnchor.constraint(equalTo: view.bottomAnchor).isActive = true  
bottomLeftGuide.widthAnchor.constraint(equalTo: view.widthAnchor, multiplier: 0.5).isActive = true  
bottomLeftGuide.heightAnchor.constraint(equalTo: view.heightAnchor, multiplier: 0.5).isActive = true

// Internal constraints, pseudo code for brevity  
someLabel.top.equalToLayoutGuideTop  
someLabel.left/right/etc

// And you can pin all the other views here that you need
```
Layout guides solve a particular problem so well that Apple has been laying them down on you for the last several releases of iOS, but you may not have actually noticed. Top layout guide, safe area layout guide — you're likely familiar with these already.

If I wasn't such a bleeding heart for writing words about coding stuff, I could've let this blog begin and end with one sentence: A layout guide can be used entirely like a dummy view but it doesn't clog your view hierarchy or respond to events.

But where is the fun in that 😛?

If you want a few extra neat tidbits about layout guide, read on.

### For De🐛-Ing

Look, Auto Layout goes to hell every now and again no matter what happens. You can either fight the wall of text spit out to the console or run and hide from it. If you opt for the former, take note of layout guide's `identifier `property.
```swift    
bottomLeftLayoutGuide.identifier = "BottomLeftGuide"
```
When things go sideways, you at least know if your layout guide is part of the issue (which is half of the battle):
```swift
// When constraints break, it'll show in the logs similar to this  
“<NSLayoutConstraint:0x6040002b8a80 UILabel:0x7fbb1a39dde0.left == BottomLeftGuide:0x6040001ae2a0.left + 16>”
```
Take note that the prefix of "NS" and "UI" are system reserved, and UIKit uses these for the layout guides that it creates.

Another technique that you can use is to query a layout guide's `layoutFrame`at runtime. There are times when one must mix both a frame based approach and Auto Layout within the same view hierarchy and this can help immensely.
```swift
CGRect guideRect = someLayoutGuide.layoutFrame  
aNonAutoLayoutView.frame = CGRectMake(0, guideRect.size.height, 100, 100)
```
Remember here though, because the layout frame is derived from Auto Layout, you'll need to ensure the constraints have been installed before this property is of any use to you. By the time the layout guide's owning view has called `layoutSubviews` — this will have the expected results.

Further, if you need to continue down the layout debugging path you may find the following two components are what you need:

* The `constraintsAffectingLayout:for:)` function
* And the `hasAmbigiousLayout` property

Using the function above, you can see all the constraints for a given axis (i.e. vertical or horizontal). Use it to see if there are any unexpected constraints influencing it:
```swift
let constraintsEffectingVertical:[NSLayoutConstraint] = bottomLeftGuide.constraintsAffectingLayout(for: .horizontal)
```
Lastly, the boolean property acts exactly how it's named. It's utility lies in the fact that you can easily check for ambigious constraints on _just _the layout guide using a symbolic breakpoint. If you are like me, sometimes you want the show to come to a total halt if something like this happens. I sometimes wrap this in an `NSAssert()` in my own side projects so I have to deal with the issue right away.

### Wrapping Up

If brevity is the source of wit, then the layout guide may be the sharpest of them all nesteled inside UIKit. It's insanely simple — just a plain old thing that looks like a view, smells like a view, acts like a view and….isn't a view. It's just a rectangular region that loves Auto Layout.

And in so many cases, that's all a developer needs. So next time you feel yourself reaching for a spacer view, opt to embrace the simplicity and safety of `UILayoutGuide`.

Until next time ✌️.

[1]: https://oleb.net/blog/2014/03/how-i-learned-to-stop-worrying-and-love-auto-layout/
[2]: https://github.com/SnapKit/Masonry
[3]: https://www.reddit.com/r/iOSProgramming/comments/4t6kd5/why_i_dont_use_autolayout/
[4]: {{ site.url | append:"/UIStackView-a-Field-Guide" }}

