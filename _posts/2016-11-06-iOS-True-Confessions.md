---
layout: post
tags: ["Misc"]
title: "iOS True Confessions"
author: Jordan Morgan
description: "There is no developer who knows it all, and in our industry imposter syndrome runs rampant. So, let's break those walls and celebrate all that we don't know."
image: /assets/images/logo.png
---
As developers — we often start our careers in a world that's directly associated with the fast pace environment of startups. After that, most all of us frequently go to conferences, speak at conferences, write a blog (🙋🏻‍♂️), teach courses and certainly dip into the dark ether of "side projects". And you know what? It's absolutely awesome, and I love it.

Be that as it may though, I'd submit to you that it's never been easier to succumb to imposter syndrome. At no other point in history have we been able to go somewhere, type a few words and find someone smarter than you within seconds (hey-oh internet!).

So as I sat down to write a technical Swift article, I found myself a bit tired mentally. Two days ago I [finished up my latest course][1], and I'm just running a bit low on the ol' tank this week.

So — what better time to celebrate the things _we've not done_ and the stuff we _don't know_ as opposed to the things we do? This week, it's iOS true confessions.

Let's dance!

_In all my years of iOS development, I've not:_

#### Used Core Data

Browse almost any Reddit post that asks for advice on frameworks to book up on before an iOS interview, and you'll most likely read Core Data. And why not? It's Cupertino & Friends**® **championed solution to manage the model layer of your application's objects. And, for my money, most of Apple's out of the box solutions work as advertised (or better).

Looking back, I'm not very surprised that I've seemingly managed to avoid any run-ins with Core Data. All of my apps didn't really end up needing much in the way of saving a model layer. Plus, if you were to concede to the general consensus, you're likely to run the other direction before you even try.

I will say that I'm not too upset about this omission. There are intuitive ways an iOS developer can persist data — writing to disk is a breeze, NSUserDefaults lets one stash user settings in a single line and going further — who isn't using a custom rolled caching strategy for API calls in their app at this point? You could make the argument that a graph management solution simply isn't as relevant as it was in iOS 3.

On the other hand — fetches can mostly be backed by NSPredicate invocations, which is fine by me. I've grown to know, love and use it for local collections for several years now. And — that's odd, because I'm not at all talented with SQL or set based programming, but I can make birds sing with good ol' NSPredicate.

It's telling, though, that there are so many elegant solutions to the same(ish) problem. Realm, which appears to be the most prominent of them - is only gaining steam with its recent [addition of its "sync everywhere" features][2]. It's been one I've enjoyed following.

Now, I'd be remiss if I didn't mention that my claim isn't entirely true. At [Buffer][3], we do use Core Data for our "drafts" feature. This was my first true interaction with Core Data. Much like anything else in programming, it wasn't anything to worry about after a few trips to the docs 📚.

Regardless,

_In all my years of iOS development, I've not:_

#### Rolled a Custom UICollectionViewLayout

Seriously though — how _awesome_ is `UICollectionViewFlowLayout` 🙌 ? It's simply worked for what I've needed to do. This out of the box class is centered around a simple line-based breaking layout. As such, flow layout places cells on a linear path, and it'll fit as many cells along that line as it can.

What's more, with a few simple assignments I can set up spacing, directional attributes, a content size and then I'm off to the next thing. If I ever needed to take things a bit further (and I haven't) — the [protocol][4] seems powerful enough for any grid layout I'd need.

Sure, as with Core Data, the only reason I've not rolled a totally tricked out collection view layout is because I haven't required one. But, still — I respect developers who have just done wonders with this thing. Pinterest, of course, comes to mind. Even though they use [ASDK][5], hit the Googles for a custom layout tutorial and most of the lot mimic a Pinterestish layout.

Now that I'm chatting about collection view is a core component I'd love to spend more time with. It's only been the last couple of years that I've come to use it more, and it's starting to steal my tired heart away from the less feature packed table view.

Anyways, let's finish our real talk out,

_In all my years of iOS development, I've not:_

#### WildCard! It Was A Long Time Before I Understood Protocols

I know, another cop out. I've been creating, teaching about and using protocols for many moons now. But, I think this one is still interesting, because it was a solid year into my iOS learnings before the lightbulb went off when it came to protocols.

And that's ironic if you know Cocoa Touch. It's packed and littered with protocols, and you need not look far for pundits who claim that there are way too many. Regardless of where you personally fall on that topic, you simply cannot deny that UIKit adores them.

But that's the "funny" part — because when one does understand protocols, they likely start laughing to themselves when they look back at iOS' unofficial "Hello World" project they no doubt made. The To-Do List app.

Oh hey 👋 — that table view thing is all up and cozy with not one, but two super common protocols!

How does it know what to do when a row is tapped 🤔 ? Is there a closure you assign to? An override? Notification? Or, could it be that didSelectRowAtIndexPath: was there waiting for you with its loving arms wide open the whole time?

That realization — that's the good part though. It solidifies your confidence that you "get" them. The rest of UIKit starts to open up a little bit. From there, the trick really becomes knowing when they make sense to use in your own endeavors.

### Wrapping Up

Starting out not only as an iOS dev, but programmer in general — I wrote two-thousand line view controllers, stored all of my local data in NSUserDefaults and adhered to the controller-controller-controller pattern. But hey, that's how you grow in this industry.

Years later, I realize now that it can be easy to view this profession as one that implores you to hide your weaknesses. I think you'll find just the opposite is true, though. After all, one of the biggest differences between a "senior" dev and a "junior" one is that the senior already knows that half of our job is constant learning, and going through the process of getting good at it.

But, one trait almost any developer shares is that we're hungry to learn and we enjoy doing it. It's about identifying the things you aren't great at and learning them, as well as teaching others about the ones you do.

Until next time ✌️.

[1]: https://www.pluralsight.com/courses/implementing-3d-touch-ios
[2]: https://realm.io/products/realm-mobile-platform/
[3]: https://itunes.apple.com/us/app/buffer-schedule-posts-for/id490474324?mt=8
[4]: https://developer.apple.com/reference/uikit/uicollectionviewdelegateflowlayout
[5]: http://asyncdisplaykit.org/
