---
layout: post
tags: ["Swift"]
title: "Protocol Extensions"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "It's possible to enhance a wide family of objects with protocol extensions. Today we'll try and see how its possible."
image: /assets/images/logo.png
---
Much has been made of Swift 2. Rightfully so, as the language indeed became more powerful and brought along several niceties that make developing software with the language very enjoyable.

That said, the topic of [protocol extensions][1] seems to be a point of contention. Is it because it's misunderstood? Perhaps engineers don't deem it necessary or useful?

It's hard to know for sure, but I would submit to you that all developers really need is just a simple, no nonsense example of it. Today, grab a cup of coffee, sit back, and let's craft such an example together.

### The Setup

I've heard it said that 60% of all statistics are made up or generally not accurate. In the spirit of that sentiment, I hereby assert that 80% of all runtime exceptions are caused by this old chestnut:
```swift
**fatal error: Array index out of range**
```
It's a common problem, and if the ever helpful [Fabric][2] is any indication — it happens far too often. If the collection is mutable, one could expect the likelihood of this problem occurring to expand by a wide margin.

So, we can either debate why that is — or we could use a protocol extension to show a practical example of how to ensure that doesn't happen. Like ever.

### The Execution

We have a problem diagnosed, now let's ponder a few things about the situation:

* We know we don't want an index out of bounds to occur when accessing Arrays
* We don't want to use inheritance to solve the issue because it doesn't scale
* We could create an extension for an Array, but that only solves the issue for just that — an Array
* So fixing arrays is great, but what about the other collections then? It'd be nice if this fixed the same problem, say, for a Set

Thus, we turn to a collection's protocol conformance to solve the problem, but also at scale. Looking at the Array, it conforms to several different protocols — around 8 of them. The promising one, however, is that of _CollectionType_.

With this in mind, one could simply solve the problem extremely quick:
```swift  
extension CollectionType  
{  
    func safeIndex(i:Int) -> Self.Generator.Element?  
    {  
        let collectionCount = Int(self.count.toIntMax())  
        guard !self.isEmpty && collectionCount > abs(i) else  
        {  
            return nil  
        }

        for t in self.enumerate()  
        {  
            if t.index == i  
            {  
                return t.element  
            }  
        }

        return nil  
    }  
}
```
With this quick and albeit dirty code, we've now ensured that if _anything_ conforms to `CollectionType` and its elements are accessed via safeIndex(i:Int) — an exception will never be thrown via index out of bounds:
```swift
let ar = [1,2,3,4]

print(ar.safeIndex(1)) //Optional 2  
print(ar.safeIndex(88)) //Nil  
print(ar.safeIndex(-34)) //Nil
```
Due to extending CollectionType, and not Array, one could even do this:
```swift
let arbitraryExample = ["Key":1]

arbitraryExample.keys.safeIndex(939449) //Yup, nil, but no exception
```
or this…
```swift
let aSet = Set(ar)

print(aSet.safeIndex(1)) //Will return an Optional Int  
print(aSet.safeIndex(-21)) //Nil
```
Even though, [as we've seen][3], order is meaningless in Set, we are still able to do this due to extending a protocol and not just a certain type.

### The Reflection

Stop to think about this for a moment. How would you have attacked this in Objective-C? It would take a much higher degree of effort and code to produce the same amount of reach as we've done here using one protocol extension.

If you've been around to see Swift evolve from V1 to V2, you've already seen all of the time protocol extensions must've saved Cupertino & friends **©**. For example, the functional aspects of Swift used to be housed in global functions:
```swift
let dibFirstRelease = contains(["Spend Stack", "Halo Timer"], {  
    $0 == "Spend Stack"  
})
```
They had a problem here. In Swift 2, it made sense to move these types of functions to belong to collection types themselves, not as a global function. And they did that (trivially) using protocol extensions.

So, instead of rewriting several collections, or using inheritance — they added contains() to everything conforming to SequenceType by writing the implementation just _once_! By adding the function to SequenceType — it's allowed us to write the code like this:
```swift 
let dibFirstRelease = ["Spend Stack", "Halo Timer"].contains({  
    $0.appName == "Spend Stack"  
})
```
Let that sink in. Apple refactored a massive amount of code with minimal effort using protocol extensions. Bravo.

### Wrapping Up

I think I'm certainly not alone when we all watched the (now legendary) WWDC15 session covering protocol oriented programming and left a little confused at the everyday application of it. It was definitely asserting that we take a much less traveled path when it comes to how we construct our software.

It took time, but I've seen firsthand how much time protocol extensions can save. And it's not just that either — you can add sweeping changes to one's codebase using this method. When you identify a time to use it, it really produces a warm feeling deep inside your hardened, cold software engineer heart.

Until next time ✌️.

[1]: {{ site.url | append:"/protocol-oriented-programming" }}
[2]: http://www.fabric.io
[3]: {{ site.url | append:"/swift-set" }}