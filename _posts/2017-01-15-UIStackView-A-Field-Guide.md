---
layout: post
tags: ["UIKit"]
title: "UIStackView: A Field Guide"
author: Jordan Morgan
description: "Stack View has been pushed heavily by Apple, but sometimes its complexity overshadows its utility. A few simple tips can help ease that burden."
image: /assets/images/logo.png
special: "true"
---
You've got to handle it to Apple's team on UIKit — they took on an intricate architectural problem and solved it rather beautifully. Given the task of creating useable APIs that would help developers create interfaces across a wide multitude of devices with differing resolutions — Auto Layout was born and thus matured.

Though the mathematical formula that constraints are born out of wasn't new, wrapping them in APIs for millions of consumers was. Be that as it may, it's no secret that NSLayoutConstraint is cumbersome, even for Objective-C standards. UIStackView helps immensely with this.

And yet for a long time, any time I used one I felt like a giant n00b 🙈.

I got that they were powerful, but boy howdy did I frequent the docs each time I slapped one down in code. They are complex in their simplicity. So this week, I thought I'd share some notes from the battlefield with my time using stack views.

### Intrinsic Sizing

This one is so easy to miss for first timers it hurts. Picture this: You become enamored with the world of iOS. The prospect of dev'n your own app keeps you helplessly awake at night. Determined, you rip through Reddit subs, online tutorials, books and videos until you've got some knowledge.

A constant emerges from each of them: _Auto Layout is necessary, but it's scary! Stack views are the answer!_

And they aren't wrong. But as a green developer, it can be confusing to understand why putting some custom control into a stack view with valid constraints doesn't work. But sometimes it doesn't, and it's usually because of intrinsic content size, or a lack thereof.

With any stack view distribution, save for .fillEqually, the stack view takes each arranged view's intrinsic size when figuring out how to lay things out on its axis. So, if you create a stack view that's pinned to the centerY and centerX of a view that's housing some custom controls — make sure it knows how to size things with a simple override in the control itself:
```swift
override func intrinsicContentSize() -> CGSize  
{  
    return CGSizeMake(200, 40)  
}
```
If we were to go with .fillEqually, intrinsic content size is tossed out in lieu of each view getting resized to fill the stack view's axis. This is a scenario where one would need to pin the stack view's constraints.

### Understanding Distribution. No, Really.

I can't believe this is so hard for my brain to quickly comprehend, but two kids later and getting 3 hour bursts of sleep at night — here we are 🙃.

Stack views know how to work their magic based on five things at the end of the day, pure and simple:

* Its given axis (vertical or horizontal)
* Its assigned alignment (center, leading, etc.)
* Its spacing, if any (a simple float value)
* Its external constraints (oh hey, I already know those, cool)
* And its assigned distribution (fill, fill equally, or proportionally, center ahhh wait what why are there so many what do they mean ahhhh)

For me, four out of five of those things I just get. And I always have. Guess the odd man out.

Here's the thing — conceptually alignment is easy for our brains to think about. If you are a vertical stack view, then how will things be aligned horizontally? I'm essentially setting their X value.

And if I'm a horizontal stack view, then my alignment tells things how to be vertically centered. Again, now I'm setting their Y value.

Distribution, though it's named insanely obvious enough — is really no different.

If it's a vertical stack view, then the enum I assign to it will determine how things stretch, size themselves or otherwise fit on a horizontal plane. If it's a CGRect I'm making, then this enum is basically supplying the width part. Now, flip flop that for horizontal stack views.

No joke, I had this comment in an app delegate file (always the dumping ground for my commented section of current To Dos ✏️) in a project for some time:

```swift
Alignment == an X or Y determination  
Distribution == how wide or tall things will be
```
And also no joke, I almost made a subclass of stack view that looked something like this:

```swift
let hStackView = HorizontalStackView()  
hStackView.verticalAlignment = .center  
hStackView.widthDistribution = .fill

let vStackView = VerticalStackView()  
vStackView.horizontalAlignment = .center  
vStackView.heightDistribution = .fill
```
It simply forwarded the assignments to a normal stack view's alignment and distribution property. But, simply keeping that in mind made the possible assignments make much more sense:
```swift
let stackView = UIStackView() //Horizontal axis by default

//Widths will be stretched to fill, usually one view takes up the majority of the space  
stackView.distribution = .fill 

//Widths are stretched to fill with the same width  
stackView.distribution = .fillEqually

//Widths are stretched to the same size to fill based off of their intrinsic content size, but they scale to keep the same proportions. Think resizing things in Sketch with the lock on.  
stackView.distribution = .fillProportionally

//Padding is used to fill out the space horizontally, but generally the views stay the same size  
stackView.distribution = .equalSpacing

//Attempts to keep the horizontal centers of each view to remain equally spaced  
stackView.distribution = .equalCentering
```
And then it all makes sense. Except there is one large piece of the puzzle that the above comments leave out.

### Resistance Priorities

Most of the time, a stack view's arranged sub views likely won't fill the entire stack view itself. So, if a stack view finds itself in such a trying predicament, it uses a mechanism you likely wrote down to research more about when learning Auto Layout but probably never revisited.

That is, content compression resistance and content hugging priority.

It makes perfect sense too, given that a majority of the controls in an iOS developer's arsenal all make use of an intrinsic content size. An easy way to wrap ones head around it is this, consider this stack view:

```swift
let stackView = UIStackView() //Horizontal axis  
stackView.alignment = .center  
stackView.distribution = .fill  
stackView.translatesAutoresizingMaskIntoConstraints = false

stackView.widthAnchor.constraint(equalToConstant: 200).isActive = true  
stackView.heightAnchor.constraint(equalToConstant: 200).isActive = true

stackView.centerXAnchor.constraint(equalTo:view.centerXAnchor).isActive = true  
stackView.centerYAnchor.constraint(equalTo:view.centerYAnchor).isActive = trueA 
```
It's 200 by 200 and in the center of a view. It's distribution strategy is to fill things out horizontally. Now, imagine if you will, it has two subviews within it, each 80 by 80.

We want to fill the stack view, but there is this extra 40 points of horizontal space hanging out. So, which one should grow? **The one with the lower content hugging priority**.

If the scenario was the same, and yet the two views were instead 120 by 120— we'd need to ask ourselves which view should become smaller in width. **The one with the lowest compression resistance priority**.

One reason you may have gotten lucky (or unlucky depending on how you see it) and missed this is because a stack view will resize a view based on index if all things are equal or ambiguous. If both views had the same values for either compression resistance or content hugging, the stack view tweaks the first view it finds in its arrangedSubviews array. This can lead to some "What the, and why?" moments if you didn't know stack views acted in such a manner.

Alas, because we aren't one for ambiguity on this blog, this is easily avoided by one, or two, simple assignments:

```swift
let aView = UIView()

//I don't want to grow in width  
aView.setContentHuggingPriority(UILayoutPriorityDefaultHigh, for: UILayoutConstraintAxis.horizontal)

//I don't want to shrink in width  
aView.setContentCompressionResistancePriority(UILayoutPriorityDefaultHigh, for: UILayoutConstraintAxis.horizontal)
```
Apple really wraps up things nicely in the docs to put the matter at rest (emphasis mine):

> When the arranged views do not **fit** within the stack view, it **shrinks** the views according to their **compression resistance priority**. If the arranged views do not **fill** the stack view, it **stretches** the views according to their **hugging priority**.

### Bonus Round

And to round things out, let's finish up with two quick thoughts.

**_One can build some padding in by way of setting an inset on a stack view:_**
```swift
let stackView = UIStackView()  
stackView.layoutMargins = UIEdgeInsetsMake(10, 0, 10, 0)
```
A without much effort, the top and bottom of your stack view will enjoy 10 points of padding on the top and bottom. Almost. Because it also requires one more assignment to a boolean property:
```swift
let stackView = UIStackView()  
stackView.layoutMargins = UIEdgeInsetsMake(10, 0, 0, 10)  
stackView.isLayoutMarginsRelativeArrangement = true
```
And then our margins appear as we'd like them to. Now to finish things out,

**_Stack views are easily used within a scroll view:_**
```swift
let stackView = UIStackView()  
scrollView.addSubview(stackView)

//A little helper I use to set top/bottom/leading/trailing constraints to another superview  
stackView.constraintsToEdges(on: scrollView)
```
Ah, but that's not enough — though it's easy to think that it should be. Due to content sizing, you need to get a little explicit sometimes with the stack view to help it understand it's width. One accomplishes this by pinning the leading and trailing constraints not only to the scroll view it's in, but also the super view containing the scroll view:
```swift
let stackView = UIStackView()  
scrollView.addSubview(stackView)

//A little helper I use to set top/bottom/leading/trailing constraints to another superview  
stackView.constraintsToEdges(on: scrollView)  
stackView.leadingAnchor.constraint(equalTo:view.leadingAnchor).isActive = true  
stackView.trailingAnchor.constraint(equalTo:view.trailingAnchor).isActive = true
```
And how you have a infinitely adaptable view that creates constraints at runtime, along with a scrollable view that resizes along with it. 2017 is going great 🎉!

### Wrapping Up

Don't ever be discouraged if something simple becomes complicated in execution for you. Sometimes, the best APIs arrive in such a state. I've come to heavily rely on stack views wherever I use Auto Layout, and they live up to the billing. Things are easier to make, faster to prototype and effortless to maintain.

Until next time ✌️.