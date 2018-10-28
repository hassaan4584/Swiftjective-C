---
layout: post
tags: ["Swift"]
title: "Initialization"
author: Jordan Morgan
description: "If you're coming from Objective-C or are new to Swift, the way its initialization works can be confusing. Diving into the particulars is a worthwhile effort."
image: /assets/images/logo.png
---
They say it's all about how you finish. Today, though, let's flip the script and instead go with a different ubiquitous motivational quote — start strong. And where does a wide margin of Swift code start? The good, old fashioned initializer, today's topic of discussion.

And I'm glad to chat about it, because when I first started Swifting* back in 2014, Init() really didn't treat me kindly. But like many relationships turned sour, we just didn't understand each other.

So this week, lean back in the sofa and close your eyes. Allow me to mend your possibly tulmutous relationship with Swift's initializers.

> *Swifting (_verb_) — To write clear and conscise code that puts a premium on type safety and initialization while asserting a severe and unapologetic prejudice against the semicolon.

### Back in My Day

Usually, confusion with Swift's initializers stem from what they _don't_ do as opposed to what Objective'C's _still_ do. The two languages don't handle initialization the same, and due to Swift's design goals which covet safety over everything else, they function entirely different.

In modern Objective-C, we'd write something like this:
```swift 
- (instancetype)initWithValue:(NSString *)value  
{  
    self = [super init];
    
    if (self)  
    {  
        self.myProp = value;  
    }
    
    return self;  
}
```
And, if you're like me, the first time you coded up a Swift initializer (without reading the docs because YOLO) you might've done something along these lines:
```swift 
init(value:String)  
{  
    super.init()  
    //Wait, what, errors already!? No base class?   
    //I don't return self?  
}
```
And this is where I realized I should just read the manual, as they kindly say. In Objective-C, I need to assign _self_ to something. In addition to returning it, it's typical to set some property values as well.

Regardless, Swift scoffs as such notions, and figures that if a new language was going to be Objective-C without the C — it should certainly apply that mantra to initializers.

### Init()

From the get go — one must change a perhaps well established approach to initializers in Swift versus how they work in Objective-C. Queue the bulleted list:

* Swift doesn't return an instance of self
* If inheritance is at play, all of the class' properties must be first initialized before calling super's initializer
* If super's initializers _is_ called, it should be called last (the opposite of Objective-C)
* …except if one is setting a superclass' property, in which case _super_ needs a chance to set it first

So what does this look like? Though that may sound like a lot of rules to follow, it generally becomes second nature after some regular Swifting. First, the basic package. Here, there is no super class so one has a little less to think about:
```swift 
class BoxReference  
{  
    var aString:NSString
    
    init()  
    {  
        self.aString = ""  
    }  
}
```
The pertinent thing to remember is Swift's accountability that it will enforce on you regarding [definitive initialization][1]. Say, for example, the previous was written like so:
```swift   
class BoxReference  
{  
    var aString:NSString
    
    init()  
    {

    }  
}
```
One would be treated with an error saying something along the lines of "Return from initializer without initializing all stored properties." This is Swift's way of saying _aString_ won't have a value, and that's a problem. So one could either create _aString_ as an optional, give it an initial value in its declaration, or assign to it in the initializer as we've done above in the prior example.

To put pen to paper (technically keyboard to screen, I guess?) — here is what those scenarios would look like — sans the last way we authored it:
```swift 
class BoxReference  
{  
    var aString:NSString?
    
    init()  
    {  
        //Valid, since aString is an optional with a default value  
    }  
}
```

_or_

```swift 
class BoxReference  
{  
    var aString:NSString = ""
    
    init()  
    {  
        //Also valid, aString has already been assigned to  
    }  
}
```
Keeping with this pattern, one could even get the initializer for absolutely free if the instance provides default values and doesn't inherit from another class:
```swift 
class BoxReference  
{  
    var aString:String = ""  
    var aBool:Bool = false  
    var aCollection:[String]?  
}

let boxRef = BoxReference()
```
Since _BoxReference_ takes care of setting all of its properties, Swift grants us a default initializer on the house. In fact, if free stuff is your thing, you can take advantage of the free memberwise initializers that come from structs:
```swift  
struct VideoGame  
{  
    var releaseDate = 1980  
    var rating = 8  
}

let aGame = VideoGame(releaseDate: 2015, rating: 9)
```
### Inheritance

For many cases, you will have Swift classes that ultimately inherit from our old friend _NSObject_. This gives us a little bit more to consider — for instance, should one want to implement _init()_ — we've now also been entrusted with overriding the superclass' implementation of it.
```swift 
class BoxRefNSObject: NSObject  
{  
    var aString:String
    
    override init()  
    {  
        self.aString = ""  
        super.init()  
    }  
}
```
It's natural for one to author the previous code by omitting the override keyword. As LLVM has progressed closer to Skynet capabilities with each release, you'll now consistently be reminded of the err of your ways in the console. Since _NSObject_ already defines it's init() method, we'll need to call out the fact that we are overriding it. Lastly — we'll then invoke the base class implementation.

### That's Super.Init()

Let's mix up the proverbial pot a bit. Consider the following:
```swift  
class Food  
{  
    var isOrganic:Bool  
    var name:String

    init()  
    {  
        self.isOrganic = false  
        self.name = ""  
    }  
}
```
You remember these, right? The atypical class definitions from CS101? Well, they are back again. If one wanted to subclass _Food_ and override it's initializer, stop and think what that would look like.

Or, just look below:
```swift 
class Pizza: Food  
{  
    var toppings:[String]
    
    override init()  
    {  
        self.toppings = ["Pepperoni"]  
        super.init()  
        self.isOrganic = false  
        self.name = "Pepperoni Pizza"  
    }  
}
```
This looks a bit crazy if you still are wearing your Objective-C glasses. Recall the rules of the land, though. _Pizza _is responsible for initializing its own properties before it worries about anything else. After that's done, it moves down the chain to the base class — but its properties can't be set until it's _init()_ has been invoked.

And why is that? For the same exact reason you set Pizza's properties before you did anything else. The class instance needs a chance to set things before anything else does, so _Food_ gets the first shot at settings its two properties, _isOrganic_ and _name_.

### For Your Convenience

The keen-eyed reader has probably had it with the way these _init()_ methods have been created. If you are longing for the concept of Objective-C's NS_DESIGNATED_INITIALIZER macro, look no further. In Swift, _init()_ is your designated initializer, and all others become convenience initializers as indicated by using the keyword:
```swift 
class Food  
{  
    var isOrganic:Bool  
    var name:String
    
    init(organic:Bool, name:String)  
    {  
        self.isOrganic = organic  
        self.name = name  
    }
    
    convenience init()  
    {  
        self.init(organic:true, name:"Tofu")  
    }  
}

//Set to some good ol' organic tofu - enjoy  
let defaultToGrossFood = Food()
```
### My Methods are Failable

One of the patterns I enjoyed in the wild west was Objective-C's ability to return nil from an initializer:
```swift 
self.url = [NSURL URLWithString:@"badurl"];

if(self.url)  
{  
    //Begin request  
}
```
It may have been quick and dirty, but like most things that fit that description — it got the job done. Swift brings us a tidy way to handle this, thanks to the large bet it has made on optionals.

Using failable initializers, brought forth in Swift 1.1, one can indicate when initialization may fail. A common scenario, and one you might've seen in your Swift endeavors, is that of UIImage:
```swift  
let invalidImg = UIImage(named: "doesntExist")

//Nil
print(invalidImg)

let validImg = UIImage(named: "exists.png")

//An optional UIImage that should be unwrapped  
print(validImg!)
```
Defining such an initializer is elementary if you're comfortable with Swift's optionals. Since my [gorgeous wife][2] has us on an all organic, anti-pizza diet before we jet off to Hawaii, consider the following:
```swift 
class food  
{  
    var isGMO:Bool
    
    init()  
    {  
        self.isGMO = false  
    }
    
    convenience init?(isAGMOFood:Bool)  
    {  
        //OH NO YOU DON'T  
        guard isAGMOFood == false else  
        {  
            return nil  
        }

        self.init()  
    }  
}
```
If the food doesn't contain GMOs, then initialization succeeds. If it does, then self is implicitly discarded. This makes initialization implementations that could fail be safe.

And also, to be honest, I don't even really know what GMOs are.

### Wrapping Up

I think Swift's initializers can be quite the buzzkill to would-be Objective-C converts. Nothing says "I'm out" quicker than writing up a subclass and not even being to get past the initializers without frustration.

But — those scenarios are symptoms of our own unwillingness to think in Swift terms. Swift != Objective-C, so it's on us to learn the ropes and get our Swift on like a true pro. And it all starts at the beginning with initializers.

Until next time ✌️.

[1]: {{ site.url | append:"/on-definitive-initialization" }}
[2]: https://www.twitter.com/jansynmorgan
