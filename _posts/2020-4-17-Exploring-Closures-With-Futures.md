---
layout: post
tags: ["Tech Notes"]
title: "Exploring Futures over Closures"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Lately, I've been researching more about effective Combine techniques. Today, I start in on utilizing Futures."
image: /assets/images/logo.png
---

One of the challenges I have faced with Combine is simply not knowing what all the framework can do, and when it should be doing it. The nascent vocabulary of its pieces combined (sorry) with my few dalliances with reactive programming has led to a steep learning curve. Even so, I've replaced `NotificationCenter` code with its built in Combine publisher, and I've enjoyed the experience quite a lot. Operators are more concise, and clarity at the point of definition is a good way to foster a readable codebase.

And yet.

What more am I missing with Combine? I've yet to author my own Publisher type, or know when the situation would call for one. I still struggle to maintain the mental model a passthrough subject affords, other than that it acts as both publisher and receiver. 

Thankfully, Apple pumped out a number of freshly minted Combine documentation, one of which led me to utilizing futures in place of closures and delegates. Personally, the benefit for me is that we harness Combine's Swiss army knife operators in lieu of boilerplate code usually found within closures and delegate patterns.

While working on [Apple Card import](https://twitter.com/JordanMorgan10/status/1250532684641775623?s=20) for Spend Stack, I had the following code:

```swift
class AppleCardImportViewController: SSBaseViewController {
    var onImport:(([AppleCardLineItem]) -> (Void))?

    private func importItems() {
        parse(csv: self?.csv) { [weak self] items in 
            guard let handler = self?.onImport else { return }

            guard !items.isEmpty else { 
                handler([])
                return 
            }
    
            let purchases = items.filter { $0.itemType != .payment }
            let translatedTags = purchases.filter { $0.category != "Other" }
                                 .map { SSListTag(fromAppleTag: $0) }

            let listItems = purchases.map { SSListItem(fromAppleCardItem: $0) }
            listItems.forEach { 
                let translatedTag = translatedTags.first { $0.name == $0.tag.name }
                if let match {
                    $0.attach(tag: translatedTag)
                }

                $0.saveAsync()
            }

            handler([items])
        }
    }
}

// Later on...
let importController = AppleCardImportViewController(source:appleCardStatement)
importController.onImport = { items:[SSListItem] in 
    // Apply to table view and local data models
}
```
While it certainly works, and I don't typically advocate rewriting what is stable - this bit of code is unreleased, so I gave myself a pass. Obviously, I've learned nothing from Spend Stack's five year development cycle 🤠. 

Here's what I came up with using a Future:

```swift
class AppleCardImportViewController: SSBaseViewController {
    func performImport() -> Future <[AppleCardItem], Never> {
        return Future() { promise in
            parse(csv: self?.csv) { items in
                promise(Result.success(items))
            }
        }
    }
}

// From the caller
let importVC = AppleCardImportViewController(withCSV: csvData)

let importCancellable = 
importVC.performImport()
        .filter { $0.itemType != .payment }
        .sink() { purchases in 
            let translatedTags = purchases.filter { $0.category != "Other" }
            .map { SSListTag(fromAppleTag: $0) }

            let listItems = purchases.map { SSListItem(fromAppleCardItem: $0) }
            listItems.forEach { 
                let translatedTag = translatedTags.first { $0.name == $0.tag.name }
                if let match {
                    $0.attach(tag: translatedTag)
                }

                $0.saveAsync()
            }

            // Apply to table view
        }
```
A few things were reconsidered, namely that I might want all Apple Card items in the future so I removed the payment versus purchase filtering. I also opted for a less strict importing function, and it does much less. 

Another implementation point I waffled on was how many operators to utilize. For example, the `sink` above could do nothing more than apply things to a table view, allowing for the `map` operator to do more of the heavy lifting. I'm not sure which I'd prefer. With Combine, there seems to be a natural tension between how much work a publisher should abstract away and then emit, versus how much of that work the subscriber should shoulder when receiving it. In a way, it speaks to the framework's utility that engineers even have the choice to begin with.

More than anything, this was a learning exercise. I'm not quite sure how I feel about supplying the publisher via a function call, which is then chained off of. Maybe it's my old Objective-C "get off my lawn" ways, I'm just not sure if that's widely accepted or not. Patterns will emerge, though, and I'm apt to take Apple at their word and sample code.

If you'd like some weekend reading, be sure to check the aforementioned sample documentation here:

- [Using Combine for Your App’s Asynchronous Code](https://developer.apple.com/documentation/combine/using_combine_for_your_app_s_asynchronous_code)
- [Routing Notifications to Combine Subscribers](https://developer.apple.com/documentation/combine/routing_notifications_to_combine_subscribers)
- [Replacing Foundation Timers with Timer Publishers](Replacing Foundation Timers with Timer Publishers)
- [Performing Key-Value Observing with Combine](https://developer.apple.com/documentation/combine/performing_key-value_observing_with_combine)


Until next time ✌️.