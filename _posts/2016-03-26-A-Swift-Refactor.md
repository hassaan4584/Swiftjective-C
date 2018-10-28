---
layout: post
tags: ["Swift"]
title: "A Swift Refactor"
author: Jordan Morgan
description: "Swift offers us several novel ways to refactor our code. Follow along as we embark on such a task on some fictional code."
image: /assets/images/logo.png
---
Fundamentals are key. Without them, you're left hedging your bets on a fragile foundation. I believe that's true for every craft and person, regardless the walk of life one hails from.

But, the rub is this: fundamentals are inherently anything but — mastering them takes time, it's also difficult and it requires a deliberate and concerted effort from a human being. In programming, specifically in terms of Swift — it begins with understanding optionals, grasping collections and embracing Swift's design decisions.

So today, we'll attempt to level up your Swift fundamentals game by refactoring a method using some Swift trickery. In short — we'll get cute.

### TheSetup.Init()

Let's take a peek at the sample specimen. In our fictitious Swift scenario, there is a function that handles the end of a round of some kind of game.

When the game concludes, some logic occurs to see if a user scored an "A+" , 5 stars, gold ribbon or whatever else badge of merit you'd like to imagine that is ubiquitous with such mobile games.

If that's the case, some logic runs with a print() for debugging purposes, and a mysterious function that nobody dares touch returns either a Struct instance or String to use as a "Congrats!" message that'll display three times.

It's rife with some weirdness, the original author is gone — and all we can do is Swift it up to be a little nicer. And Swift it we shall.

Without further ado, meet your adversary:
```swift
//Assume PlayerRound is a simple struct with a roundScore Int  
//CongratsMessage is also a struct with a roundCongrats String

let aRound:PlayerRound? = PlayerRound()

if aRound != nil  
{  
    if aRound?.roundScore? >= 5 && aRound?.roundScore? <= 10  
    {  
        print("Score gets an A+")

        let iterationNum = [1,2,3]  
        for i in 0…iterationNum.count  
        {  
            print("Awesome, we are showing a victory message!!")

            let message = self.randomCongrats()  
            switch message  
            {  
                case let message where message is String:  
                print(message)  
                case let message where message is CongratsMessage:  
                print(message.roundCongrats)  
                default:   
                print("Uh oh")  
            }  
        }  
    }  
}
```
As you can see, there's work to be done here. Let's dance.

### Round 1: Pattern Matching

The first thing you notice is the double conditional if statement. It would seem, in this beloved fake game, that if one scores anywhere between 5 points and 10 they are bequeathed the revered A+ badge. While the logic is sound, and though you aren't too crazy about the magic numbers — you see a chance to strike.

Enter the ~= operator, pattern matching to the core. When it's all said and done, the ~= matches two values of the same type, so who is to say we can't match between a range of Ints?

Since you notice that aRound won't be nil, you refactor this:
```swift 
if aRound?.roundScore? >= 5 && aRound?.roundScore? <= 10
```
To this:
```swift
if 5…10 ~= aRound!.roundScore!
```
Boom — we're already chippin' away at this thing! Our first edit is cemented, but this just gives you more drive. There are more to come.

### Round 2: Intuitive Optional Unwrapping

Hmm, you aren't fond of force unwrapping those values. Even though you know they aren't nil, still — it just feels a bit off? You assert that this is a time to unwrap optionals in the traditional way.

For the first pass, we take this out:
```swift 
if aRound != nil
```
And go with the more familiar:
```swift 
if let round = aRound
```
And, that does feel a bit better. However, looking at the method we realize the only thing we care about is the score held in aRound. So, you roll up your sleeves and dish out a combo:
```swift   
if let playerScore = aRound?.roundScore where 5…10 ~= playerScore
```
Presto! This function doesn't even do anything if the sweet spot of 5–10 isn't met, so why not unwrap an optional and move the range check all in one?

Again, this just adds more refactoring fuel to your proverbial fire. You continue.

### Round 3: The Odd Array

At this point you turn your attention to the spot where a "Congrats!" message is displayed a few times in a loop. But — you notice an oddity.

Could it be? A wholly random and untimely Array?
```swift 
let iterationNum = [1,2,3]  
for i in 0…iterationNum.count
```
It seems it's only used to create a bounds. And, who knows the story here? Did the original developer have a different plan, and then later course correct and forget about this? Did others see it as well, and then have the same thought but ended up not touching it because it feels….like a trap of some sort?

Who knows — but you're unshaken. Your first gut reaction is to tighten up the Array initialization:
```swift  
let iterationNum = Array(1…3)
```
You think, "It's just numbers! I can use a range to initialize it!" — and that's certainly true. You go further and realize the index is never used, the intention seems to just be printing a message 3 times. So, you bust out the wildcard instead of the "i":
```swift 
for _ in 0…iterationNum.count
```
And after that, you realize the root of the problem. The Array doesn't even need to be there, we could just write it like this:
```swift
for _ in 0…3
```
….and you do, but then another thought sweeps into your head:

### Round 4: Fun with Extensions

What if we need to do a few things a set number of times going forward? What if we had a pragmatic and uber readable way to do something like that?

You smile at the computer and take another sip of your lukewarm coffee. You have just the thing:
```swift  
extension Int
{
    func times(task: () -> ())
    {
        for _ in 0..<self
        {
            task()
        }
    }
}
```
Due to Swift’s accessible approach to retroactive modeling, you just crafted a simple way to do numbered operations. And so it is, you insert yet another refactoring win:

This:
```swift
for i in 0…iterationNum.count
{
    print(“Awesome, we are showing a victory message!!”)
    let message = self.randomCongrats()
    switch message
    {
        case let message where message is String:
             print(message)
        case let message where message is CongratsMessage:
             print(message.roundCongrats)
        default: 
             print(“Uh oh”)
    }
}
```
Goes to this:

```swift
3.times
{
    print(“Awesome, we are showing a victory message!!”)
    let message = self.randomCongrats()
    switch message
    {
        case let message where message is String:
             print(message)
        case let message where message is CongratsMessage:
             print(message.roundCongrats)
        default: 
             print(“Uh oh”)
    }
}
```
….hey oh!

### Round 5: Nil Coalescing Operator
While you were close to pumping your fist on your finest refactor of the week, your heart quickly skips a beat - a runtime exception! The already troublesome randomCongrats() function seems to be returning a nil value sometimes!
After a quick check at it’s signature, sure enough — your thoughts are confirmed:
```swift
func randomCongrats() -> AnyObject?
```
Argh! You go to tighten up the function only to find it's a thousand lines! You'd rather stay away — anymore dancing in there could do more harm than good. So, you look to solve the problem here.

Perhaps we fix it via optional unwrapping on the message variable? It's an option, but a nifty operator comes to mind: the nil coalescing operator.

We rewrite this:
```swift
let message = self.randomCongrats()
```
…to instead go with this:
```swift
let message = self.randomCongrats() ?? "Woohoo - great score!"
```
And we move on to the next win, because we know nil values will never weasel their way in front of our function's execution hereafter. If randomCongrats() nils us up, we'll set the message to a friendly default string.

### Round 6: Tighter Type Checks

Now that we've sort of dealt with the nil value, we gaze upon the switch statement. It seems as though we've just got to deal with the AnyObject? return type, so checking the type at runtime will occur regardless.

But, [we frequent Twitter][1]. And because of that, we know we can at least sprinkle a little bit of syntactic sugar on it:
```swift  
3.times  
{  
    print("Awesome, we are showing a victory message!!")
    
    let message = self.randomCongrats() ?? "Woohoo - great score!"  
    switch message  
    {  
        case is String:  
        print(message)  
        case is CongratsMessage:  
        print(message.roundCongrats)  
        default:   
        print("Uh oh")  
    }  
}
```
Nice — no need for all of the let x where x is y syntax. We're going places — but to really top it off, you find one more piece of low hanging fruit.

### Round 7: The Touch Up

The next fix you wish to implement? It's small, almost inconsequential. But, does it ever just give it that last finishing touch.

Those little debug messages:
```swift  
print("Score gets an A+")
```
and
```swift 
print("Awesome, we are showing a victory message!!")
```
You recall that you've heard talk that a robust logging framework is about to be put in place. Perhaps that explains the sporadic print() statements you've been seeing. Either way, it's time to take things to 10.

You take them both out, and replace it with some information that could actually be helpful down the road:
```swift 
print("Invoking (#function) on (#line)")
```
You're an astute Swift developer — and you remember that the screaming snake case symbols (i.e. \__FUNCTION__) have been retooled for Swift 2.2. Now, with these little numbers you just included, the compiler will actually print out the function name and the exact line. Ammo we can use.

You're tempted to include #file too, but you know you've done enough at this point. The new function looks much more Swiftier, and you're happy with it!

### The final product
```swift 
if let highScore = aRound?.roundScore where 5…10 ~= highScore  
{  
    print("Invoking (#function) on (#line)")
    
    3.times  
    {  
        let message = randomCongrats() ?? "Woohoo — great score!"

        switch message  
        {  
            case is String:  
            print(message)  
            case is CongratsMessage:  
            print(message.roundCongrats)  
            default:  
            print("Uh oh")  
        }  
    }  
}
```
…and then you send your pull request with a warm smile, knowing you've just been filled with the feeling of accomplishment that can only be evoked from the pure thrill of software engineering.

### Wrapping Up

In all seriousness and imaginary scenario aside, Swift has a lot of bells and whistles, and it's likely no one person will ever know them all.

But we can know some of them.

And knowing some of them leads to a lot of fun, cleaner and more expressive code. True, there are more pressing problems with our sample func (the type checking, the ominous function returning a String or a Struct, etc.), but the point is — sometimes I.R.L. you don't have control over those things.

But, most of the time we can Swift them up to be a bit nicer.

Until next time ✌️.

[1]: https://twitter.com/terhechte/status/711357380479426560
