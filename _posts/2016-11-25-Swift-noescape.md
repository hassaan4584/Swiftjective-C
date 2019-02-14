---
layout: post
tags: ["Swift"]
title: "noescape"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "It's an attribute you've seen hanging over closures. Let's dive into what it means, or more specifically - meant."
image: /assets/images/logo.png
---
With the advent of Swift 3, I think a lot of iOS engineers are still basking in the many [accepted changes][1]. For both better and worse, if you are riding the Swift train it can sometimes feel like you're trying to develop software while riding a bike downhill and blindfolded. That's the buy in for change, and I'm personally all for it.

As many of us likely did, I kept hammering away inside of Xcode until my projects would build. Some refactors were simple (goodbye NS prefix 👋) and some weren't (new GCD syntax is amazing, but it took a few trips to the docs to get it right). And some — some we just skipped right past and took whatever Xcode said as gospel.

And one likely candidate for this? Good ol' (now deprecated) @noescape — this week's topic.

### It's Closure Time (…You Don't Have To Go Home 🎶)

This little attribute is something you may have noticed popping up in method signatures which took in a closure as a parameter. Before, one could likely author such signatures like so:
```swift
func asyncOp(completion:()->Void)  
{  
    //Do some async networking call and then invoke completion()  
}
```
And that's fine — but true to Swift, expression and intent are absolutely important. With one of the language's core pillars being readability, it stands to reason that the more Swift evolves — the more readable it becomes. Clarity. Uniformity. No ambiguity.

So — what's that have to do with the @noescape attribute? And why are we talking about it if it's already deprecated?

### What it Means To Escape

First off, can I just say that the title of this header tag sounds like a perfect name for an emo song in the mid-2000s 🤘?

Moving on, to develop a vocabulary around this particular attribute and what it meant, we need to think about why we would've used it to begin with. I've seen it explained many ways, but essentially it boils down to this: **timing and scope**.

As in, will the passed in closure be called _post_ function invocation? If the function _returns_ but we call the closure at a _later_ point in time in the execution flow — then it's most certainly escaped that function 🏃.

Though conversely, if we only expect that function to do its thing within the function's scope — then it's not getting its grimy mittens on anything else. Thus, it's a non-escaping closure. Typically, this is the common use case. The question then becomes, when is it not?

### Async for Days

As iOS devs — we've got quite the unrelenting thirst for HTTP networking in our apps. We do love us a good cup of JSON throughout our workday. Be that as it may, you no doubt are familiar with the uncertainty that comes with such territory.

Is the response cached? Is the network strong? Is this our first trip to the server? The list goes on.

So, say we've got an array typed to contain basic closures as a property on some object:
```swift
var onFakeCompletions:[()->()] = []
```
Down the line, we've got a network operation that takes in a closure that executes upon the response coming back:
```swift
func fakeNetworkOp(_ completion:()->())  
{  
    //Network stuff happens  
    completion()  
}
```
If we were to pass in the first element in the onFakeCompletions property, that would be certainly valid as it matches the function's formal parameter list just fine. But, one of the most common ways a closure escapes a function is by being stored in a property. A lot of the times, this isn't an issue or the scenario. Plus, you're likely to see a lot of trailing closure syntax:
```swift
func fakeNetworkOp(_ completion:()->())  
{  
    //Network stuff happens  
    completion()  
}
```
...later on

```swift
fakeNetworkOp { print("Using a trailing closure, not a property") }
```
The closure above won't be tinkered with anywhere else, inherently — it's already non-escaping.

But, if we do something like this — then it could be:
```swift
func fakeNetworkOp(_ completion:()->())  
{  
    //Network stuff happens  
    onFakeCompletions.append(completion)  
}
```
The property we append to is declared _outside_ the scope of the function, so here the completion argument must be declared as escaping as it could be invoked later on:
```swift
func fakeNetworkOp(_ completion:@escaping ()->())  
{  
    onFakeCompletion.append(completion)  
}
```
Now, when we come back to maintenance this code, we immediately can reason that the closure being passed in will likely be used after the function returns and calls it a day.

In the end, what you'll often find is that escaping closures will make a reference to self within the closure's execution.

### Default to Safety

The good news is —[ @noescape is the default value][2] now. As previously mentioned, it's already deprecated if one tries to throw it in a function signature. Lipso facto, most of the time you don't need to do anything.

But, that means Swift will make you think if you _do_ need to make a closure escaping. While a friendly compiler error will say hi to you if you omit it where it's needed, that's all well and good because if you're there — you likely have some sort of anti pattern in the works. Not always, but probably.

### Wrapping Up

Our now deprecated friend @noescape isn't so scary or mystifying once it has been put under the fine microscope of a simple blog post. In fact, the little fella' was quite welcome, as I'm sure you'd agree that its presence helped Swift be more Swifty by virtue of bringing more clarity to our code.

Further more, it's a necessary tool too, what with the async world we live in now. Who isn't hitting a backend or dancing around an API at this point? We're past simple tipping calculators and flashlight apps in today's ecosystem. As such, we need new tools and constructs to help us craft quality software. @noescape lives on by being the default choice and his older brother @escaping is still hanging around.

Until next time ✌️.

[1]: https://swift.org/blog/swift-3-0-release-process/
[2]: https://github.com/apple/swift-evolution/blob/master/proposals/0103-make-noescape-default.md
