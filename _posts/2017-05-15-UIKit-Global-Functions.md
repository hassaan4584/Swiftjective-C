---
layout: post
tags: ["UIKit"]
title: "Global Functions"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "UIKit has everything to splash out eye candy. But it also houses several helpful utilities to help get you there."
image: /assets/images/logo.png
---
On the beaten path of iOS development, across many MacBooks and iMacs, lies an extremely worn in trail forged by thousands of engineers the world over who've come before you. Like them, one of your first stops in the boundless world of iOS development is UIKit — Apple's accessible and tested answer to creating first class user interfaces.

As the years go by, I've found that there is always another little nugget that lay hidden away and waiting to be discovered within it. So this week, we'll look at both the obvious and obscure functions UIKit provides to help us cut corners ✂️.

### [Gettin' Stringy With It (Na Na Na Na Na 🎶)][1]

Perhaps no tool is more necessary for effective iOS development than LLDB. No developer will ever bat 1.000 — bugs are as much a part of iOS development as much as oxygen is to breathing.

Often times in my debugging sessions, I'll need to query values relating to view geometry. What size is this view? Where is it at? What's its transform value looking like?

Instead of doing this:
```swift
let aView = UIView()

print("aView's x is (aView.frame.origin.x) and it's size is width:(aView.frame.size.width) height:(aView.frame.size.height)")

//Prints "aView's x is 0.0 and it's size is width:0.0 height:0.0"
```
Let this handy function do the heavy lifting:
```swift
let aView = UIView()

print("aView's frame is (NSStringFromCGRect(aView.frame))")

**//**Prints** **"aView's frame is {% raw %}{{0, 0}, {0, 0}}{% endraw %}"
```
Fortunately, the applications are not limited to just viewing the coordinates and size of a CGRect structure. Essentially every piece of UIGeometry (and beyond) has a shortcut to format its contents:

* `NSStringFromCGAffineTransform()`
* `NSStringFromCGPoint()`
* `NSStringFromCGRect()`
* `NSStringFromCGSize()`
* `NSStringFromCGVector()`
* `NSStringFromUIEdgeInsets()`
* `NSStringFromUIOffset()`

If you'd rather avoid the Objective-Cish verboseness of the signature (which I personally don't mind), feel free to hang these off of simple extensions:
```swift
extension UIView  
{  
    func formattedFrame () -> String  
    {  
        return NSStringFromCGRect(frame)  
    }  
}
```

...later on
```swift
print(aView.formattedFrame())
```
### Conversions

Aside from formatting instances into human readable strings, one can also go the opposite direction and provide strings to generate certain types.

I personally swear by some of these methods — as I find the one time it pays to be "stringy" with your APIs and code is when readability can be maintained along with compile time checks.

Let's look at a contrived example 🙃:
```swift
let preferredSizes = ["{4.0,6.0}","{5.0,3.0}","{4.0,6.0}"]

for size in preferredSizes  
{  
    print("Size: (CGPointFromString(size))")  
}

//Prints "Size:(4.0, 6.0) Size: (5.0, 3.0) Size: (4.0, 6.0)"
```
So long as one supplies a string formatted with curly braces and double values within, a valid CGPoint rect is created. For example, {width,height} in our code sample above.

The same pattern holds true for both points and wholly formed CGRects. Safety is guaranteed as well, since an incorrectly formatted string will yield "default" values for each type (i.e. .Zero for CGRect):
```swift
//A correctly formatted string  
let aRect = CGRectFromString("{% raw %}{{10,10},{100,100}}{% endraw %}")  
print("(NSStringFromCGRect(aRect))")  
//Prints "{% raw %}{{10, 10}, {100, 100}}{% endraw %}"

//Incorrectly formatted  
let invalidRect = CGRectFromString("wupps")  
print("(NSStringFromCGRect(invalidRect))")  
//Prints "{% raw %}{{0, 0}, {0, 0}}{% endraw %}"
```
It's not all or nothing, either. If only one value is acting up, UIKit dutifully returns a 0 value for the given argument while the rest still hold up:
```swift
//Here, only the origin.x is invalid  
let invalidX = CGRectFromString("{% raw %}{{YOLO,10},{100,100}}{% endraw %}")

print("(NSStringFromCGRect(invalidX))")  
//Prints "{% raw %}{{0, 10}, {100, 100}}{% endraw %}"
```
You may also notice the literal string format from your Objective-C days, where in some instances it was _just _a wee bit easier to do things similar to this rather than use `CGRectMake()`:
```swift
CGPoint viewOrigin = view.frame.origin;  
CGSize viewSize = view.frame.size;

CGRect viewFrame = {% raw %}{{viewOrigin.x, viewOrigin.y}, {viewSize.width, viewSize.height}}{% endraw %};
```
When the situation calls for it — I enjoy using these functions for quick frame logic. You can even create entire transforms with it using the same syntax (i.e. "CGAffineTransformFromString("{a, b, c, d, tx, ty})").

Imagine if you needed to create a simple view from an API call, there'd be no need for an update or some Javascript live patch workaround. It'd (theoretically) be little work with a JSON response that looked something like this:
```swift
{  
    "viewOne":   
    {  
        "frame": "{% raw %}{{10,10}, {100,100}}{% endraw %}"  
    }  
}
```
And a network request:
```swift
typealias JSONDictionary = [String:String]

let data: Data = Data() //From the JSON response  
let viewJSON = try? JSONSerialization.jsonObject(with: data, options: [])

if let dictionary = viewJSON as? JSONDictionary  
{  
    for (key, value) in dictionary  
    {  
        if let viewOne = dictionary[key] as? JSONDictionary   
        {  
            let theView = UIView(frame: CGRectFromString(viewOne["frame"] ?? "{% raw %}{{0,0},{0,0}}{% endraw %}"))  
        }  
    }  
}
```

### Being (Not So) Adaptive

Even though Apple will quickly remind us that the device and orientation shouldn't hold nearly as much weight as it used to with the advent of adaptivity APIs introduced in iOS 8, let's be real.

Sometimes, I really do just want to know if the binary is chillin' out on an iPad, feel me?

If you're new(ish) to the game, you not know about this old tool once widely wielded by the iOS veteran:
```swift
if UI_USER_INTERFACE_IDIOM() == .pad  
{  
    //iPad specific logic woohoo 📱!  
}
```
Though Apple only recommends such an approach for apps running on iOS version _3.2._

**Every dev in the room, raise your hand if you're still targeting iOS 3.2!! Nobody? No? Okay.**

Jokes aside, sometimes I find it incumbent to query things like device and orientation in `viewWillTransitionToSize:withTransitionCoordinator:` and it's trivial with UIKit's functions for doing just that:
```swift
if UIDeviceOrientationIsPortrait(UIDevice.current.orientation)  
{  
    // Tweak some of that outlier pixel perfect logic code  
}
```

### Danger Zone

With today's JSON driven development world, (J.D.D. has to be acronym already, right?) it's common to send some media over the wire. And what better way to send it off than packaging an image into a tidy, neat packet of NSData?

UIKit has you covered, with two functions to return a nullable instance of data as a .png or .jpeg:

```swift
let anImage = UIImage()

let pngImgData = UIImagePNGRepresentation(anImage)  
let jpegImgData = UIImageJPEGRepresentation(anImage, 1)
```
Both are handy, so long as the image has a valid bitmap or CGImageRef, NSData is produced right away. The .jpeg variant, sensibly, allows for a variable amount of compression as well (0 meaning compress it to hell and back and then once more, and 1 meaning keep it pristine and lossless).

But here be dragons 🐉.

Good ol' `UIImagePNGRepresentation()` can be notorious for returning a disproportionally large amount of data back. Should .jpeg not be an option, one might have to get a bit creative and use `CGImageDestinationAddImage()` as a workaround.

### Accessibility

Perhaps the most critical, and criminally under utilized, functions within UIKit pertain to accessibility. There are many avenues to check whether the user doesn't want blurring to occur, if voice over is running and more.

No dancing around here, just simple functions that return a boolean. There are several accessibility values than be queried, but in my opinion some often overlooked considerations should be made in first class apps for at least the following:
```swift
if UIAccessibilityIsReduceTransparencyEnabled() { //Kill blurring }

if UIAccessibilityIsVoiceOverRunning() { //Flow considerations }

if UIAccessibilityIsReduceMotionEnabled() { //Kill parallax }
```
### And The Rando

It's not common, but there are cases that call for taking advantage of [guided access][2] within iOS. In such scenarios, the restriction state by default is set to .allow. If one has, and likely does, have conditional logic in place, the restriction state can be queried via UIKit's public function:
```swift
if UIGuidedAccessRestrictionStateForIdentifier("someIdentifier") == .deny  
{  
    //Keep some restriction in place  
}
```
By the same vein, one can ensure guided access is running all together:
```swift
if UIAccessibilityIsGuidedAccessEnabled() { //It's on }
```
### Wrapping Up

I've been a big fan of writing "the more you know" type of articles on this blog for some time now. Sometimes I argue myself out of it, since I reason that the wily veteran likely has seen the alleged shortcut many times before.

But then I think, why not? What makes a software engineer's day more than finding a nifty new piece of code tucked away in a well known framework? Experienced developer or fledgling novice, there's always more to find.

It's about shaving off little bits of time, and more importantly writing a little bit less code that's just as descriptive, by crackin' out every item we've got in our tool belt. And, as we've seen, UIKit provides many such tools in the form of its class functions to check out frames all the way to creating entire PDF documents. Check out the full list [here][3].

Until next time ✌️.

[1]: https://www.youtube.com/watch?v=3JcmQONgXJM
[2]: https://support.apple.com/en-us/HT202612
[3]: https://developer.apple.com/reference/uikit/uikit_functions#//apple_ref/c/func/UIGraphicsBeginImageContextWithOptions