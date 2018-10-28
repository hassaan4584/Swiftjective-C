---
layout: post
tags: ["UIKit"]
title: UIGraphicsImageRenderer
author: Jordan Morgan
description: "For years, we've used Core Graphics to draw. Let's turn the page to modern drawing on iOS."
image: /assets/images/logo.png
---

The history of photography is rife with interesting stories of how the medium developed. Among them, digital photography is one of the more exciting bits of its history. It's still quite a nascent craft, with its history tracing back to as recently as 1957 when the fine folks at the National Institute of Standards and Technology slapped a photo into computer memory.

The translation from the analog to the digital was an inflection point. We've experienced a similar shift on iOS starting with iOS 10, though many engineers have yet to discover or adopt the latest innovation for drawing images — `UIGraphicsImageRenderer`.

### Setting the (CG)Context
Core Graphics, based on the Quartz drawing engine, has provided iOS developers with lightweight 2D rendering capabilities since iOS 2. Its utility knows almost no bounds, as image masking, PDF document creation, parsing, and other similar functions are baked right in making it a no nonsense choice for any sort of drawing task.

For that and many other reasons, if one hits the Googles on how to create an image from something on screen they'll likely end up with something like this:
    
```swift
let drawSize = CGSize(width: 20, height: 20)

UIGraphicsBeginImageContext(drawSize)  
let ctx = UIGraphicsGetCurrentContext()!

ctx.setFillColor(UIColor.red.cgColor)  
ctx.fill(CGRect(x: 0, y: 0, width: drawSize.width, height: drawSize.height))

let img = UIGraphicsGetImageFromCurrentImageContext()
```

And it works, so we move on. Though, there are several valid reasons to pump the breaks:

* UIGraphicsBegin/EndImageContext are sRGB only (sorry p3 color gamut).
* It was before the age of blocks, which are common throughout Foundation, UIKit and virtually every framework on iOS.
* Extensibility is possible, though sometimes a non trivial task.

Given its age, it's not a shocker to say Core Graphics provides an API that's less than ideal too many of today's standards. Though Swift's syntactical sugar prowess has softened the call sites to Core Graphics code over many projects, it still is what it is — a C based API built for simpler times.

In contrast, `UIGraphicsImageRenderer` is built for tomorrow in mind:

* It's automagically fully color managed. For example, on the beautiful 9.7 inch iPad pro you'll get a wide color context.
* It's a first class object.
* It manages its context lifetime, unlocking some memory optimizations on the house from Cupertino & Friends©.
* The former implicitly means that it caches its context, meaning reuse is an efficient operation as opposed to using new renderers.

### Gaining More (CG)Context

Initializing and keeping a reference to a renderer is a solid start:
    
```swift    
let renderer = UIGraphicsImageRenderer(size: CGSize(width: 20, height: 20))
```

From there, the relevant parallel from the old way of doing things to the preferred, Apple approved way would be image renderer's closure based functions for creating an image:
    
```swift
func image(actions: (UIGraphicsImageRendererContext) -> Void) -> UIImage
```

To compare apples to image renderers, one could create the same image as mentioned above from the legacy Core Graphics method by doing this:
    
```swift
let img = renderer.image { (ctx) in  
    let size = renderer.format.bounds.size  
    UIColor.red.setFill()  
    ctx.fill(CGRect(x: 0, y: 0, width: size.width, height: size.height))
}
```

The hard work of what's happening here has always been abstracted away by Core Graphics since day one, but now it's more honed in to the point where we simply spit out some drawing instructions within a block.

The renderer also exposes convenient access to getting a hold of `NSData` of resulting images as well:
    
```swift    
let actions:(UIGraphicsImageRendererContext) -> Void = { (ctx) in  
    let size = ctx.format.bounds.size  
    UIColor.blue.setFill()  
        ctx.fill(CGRect(x: 1, y: 1, width: size.width - 1, height: size.height - 1))  
}

let imageJPEGData = renderer.jpegData(withCompressionQuality: 1, actions: actions)
let imagePNGData = renderer.pngData(actions: actions)
```

In each code sample, the typealiased `DrawingActions` closure returns to us an instance of `UIGraphicsImageRendererContext`. Using it we gain access high-level drawing functions. Though Apple clearly states "higher level" drawing functions, don't think of it as a crutch. There is support for most drawing tasks, such as utilizing blend modes by leveraging `CGBlendValue`:
    
```swift  
let image = renderer.image { (ctx) in  
    UIColor.blue.setFill()  
    ctx.fill(CGRect(x: 1, y: 1, width: 140, height: 140))

    UIColor.yellow.setFill()  
    ctx.fill(CGRect(x: 60, y: 60, width: 140, height: 140), blendMode: .luminosity)  
}
```

That said, you may be left missing the drawing functionality you might've thought left behind from the traditional context.

For example, filling in an ellipses still requires a R.O.C.G.C. (regular old Core Graphics Context, obviously). To fill out the drawing functionality (pun somewhat intended), an image renderer context has one available.

Take note of the last two lines, where the `cgContext` allows us to fill out the circle:

```swift
let img = renderer.image { (ctx) in  
    let size = ctx.format.bounds.size

    UIColor.darkGray.setStroke()  
    ctx.stroke(renderer.format.bounds)

    UIColor.blue.setFill()  
    ctx.fill(CGRect(x: 1, y: 1, width: size.width - 1, height: size.height - 1))

    UIColor.yellow.setFill()  
    ctx.cgContext.fillEllipse(in: CGRect(x: 51, y: 51, width: size.width/2, height: size.width/2))  
    ctx.cgContext.rotate(by: 100)  
}
```

### Giving a Renderer More (CG)Context
I really need to stop with the (CG)Context bit, but I feel too invested at this point so please just excuse me 🤠.

You have noticed that a graphics renderer will also accept a `UIGraphicsImageRendererFormat`object into two of its four available initializers:
    
```swift  
public init(size: CGSize, format: UIGraphicsImageRendererFormat)  
public init(bounds: CGRect, format: UIGraphicsImageRendererFormat)
```

This rendering format has a few options to aid in further specifying the intent of your resulting drawing operations. It also has a useful `bounds `property we've been using in the previous code samples that's derived from its associated graphics context. Using this formatter one can tweak opaque or scale preferences, among other things.

For example, `CALayer` and its A8 backing store format was introduced in iOS 12 and provides developers with free memory optimizations. If you're certain, for example, that you're drawing wide color content _using_ sRGB colors, you can have the renderer optimize for that since the backing store would otherwise be larger to accommodate a larger color range rather than just 0 to 1:
    
```swift
// iOS 10/11
let format = UIGraphicsImageRendererFormat()  
format.prefersExtendedRange = false

// iOS 12
let format = UIGraphicsImageRendererFormat()  
format.preferredRange = .standard // Turn off iOS 12 optimization
```

Many of these decisions will likely be tied to the current trait collection, so it stands to reason that the renderer format can also be fetched on a per trait collection basis as well.

No need to mince in my own words here, Apple's documentation explains this very well:

```swift
// Returns a format optimized for the specified trait collection, taking into account properties such as displayScale and displayGamut.
// Traits that are not specified will be ignored, with their corresponding format properties defaulting to the values in preferredFormat.  
public convenience init(for traitCollection: UITraitCollection)
```

No worries if you opt to forgo any of this, as UIKit provides sensible default values for you should you not provide explicit ones. As such, if you do nothing, UIKit gives you the resulting format from its factory method, `defaultFormat` — which provides a format configured for the highest fidelity possible as supported by the device it's executed on.

Take care to make this choice upfront, however. If you want to configure things, do it at your renderer's initialization point as the formatter itself holistically represents immutable configurations that it will always use during drawing operations.

All of this hopefully should remind you how extensible and flexible an image renderer can be. For example, hanging a quick extension off of any view to create a circle avatar would be painless and performant (as performant as using `cornerRadius` can really be, that is) since one could reuse the same renderer and its context:
    
```swift
private var rendererKey: UInt8 = 0

extension UIView {
    var renderer: UIGraphicsImageRenderer! {  
        get {  
            guard let rendererInstance = objc_getAssociatedObject(self, &rendererKey) as? UIGraphicsImageRenderer else {  
                self.renderer = UIGraphicsImageRenderer(bounds: bounds)  
                return self.renderer  
            }

            return rendererInstance
        }  
        set(newValue) {  
            objc_setAssociatedObject(self, &rendererKey, newValue, objc_AssociationPolicy.OBJC_ASSOCIATION_RETAIN)  
        }  
    }

    func circleImageView() -> UIImageView {  
        let img:UIImage = renderer.image { ctx in  
            layer.render(in: ctx.cgContext)  
        }

        let imageView:UIImageView = UIImageView(image: img)  
        imageView.frame = renderer.format.bounds  
        imageView.clipsToBounds = true  
        imageView.layer.cornerRadius =(renderer.format.bounds.width/2).rounded()  
        return imageView  
    }  
}

// Generate a circle image and image view of any view instance  
let anImageView = myExistingView.circleImageView()
```

### PDFs FTW
A quick sidebar to mention that the PDF variant of the abstract `UIGraphicsRenderer` class is very similar to its image rendering sibling. In fact, their method declarations are almost interchangeable, save `UIImage` vs `Data`:
    
```swift  
let renderer = UIGraphicsPDFRenderer(bounds: view.bounds)  
let pdf = renderer.pdfData { (ctx) in  
ctx.beginPage()
    let header = "Welcome to TTIDG!" as NSString  
    let attributes = [  
        NSAttributedStringKey.font : UIFont.preferredFont(forTextStyle: .body),  
        NSAttributedStringKey.foregroundColor : UIColor.blue  
    ]

    header.draw(in: CGRect(x: 0, y: 0, width: ctx.pdfContextBounds.width, height: ctx.pdfContextBounds.height), withAttributes: attributes)  
}
```

### Wrapping Up
Replacing the code that kinda just works with the code that's more recent and supports more relevant formats is typically not high on the proverbial list.

Maybe it should be, as is the case with `UIGraphicsImageRenderer`. You likely won't have to twist many arms to persuade iOS engineers to make the switch, "No ✋ — I don't want block based, automatically color managed, extensible drawing code that already manages its context lifetime — that's awful" said…..nobody?

Until next time ✌️.

  