---
layout: post
tags: ["Swift"]
title: "Looping and Iteration"
author: Jordan Morgan
description: "It's one of the first thing green programmers learn - looping and iteration. Swift has made it easy and versatile."
image: /assets/images/logo.png
---
History repeats itself. And so does code. Frequently.

Looping and iteration are core not only as a language feature, but to programming itself. They are critical to understand, but thankfully they are common and required techniques which manifest themselves slightly different across languages. In this regard, swift should be commended, because its looping constructs are phenomenal.

So this week, let's talk control flow.

### For Loop

The basic C-style for loop. Well known and used. For definition purposes, a for loop operates a fixed number of times — typically executing a set of statements until a counter has been satisified. Unlike it's C forefathers, parenthesis are not required around the loop declarations.

Its body looks like this:
```swift
for initialization; condition; increment  
{  
    statements  
}
```
While in practice you'll see something along these lines:

```swift
//Prints 0 through 8  
for var i = 0; i < 9; i++  
{  
    println(i)  
}
```
Although the parenthesis are not required, they are certainly legal. Call me old fashioned, but I tend to prefer them for syntactic sugar. Old habits die hard, as they say.

### _While_ You're Here

Technically speaking, the previous example is equivalent to performing a variant of the _while-loop_. From our example above, doing the following will yield the same result:

```swift
var i = 0  
while i < 9  
{  
    println(i)  
    i++  
}
```
The while is close friends with the do-while loop. Its usefulness lies in cases where code must be evaluated at least _once._
```swift
var x = 0  
do  
{  
    println(x)  
    x++  
} while x < 9
```
### Closed & Half Open Range Looping

Because swift is sort of in to giving developers new ways to solve old problems, Cupertino and company graced us with two new range operators. If you haven't been formally introduced, allow me to show you the closed range loop:
```swift
//Closed range, prints 10 times, up to and including 9  
let count = 9  
for index in 0…count  
{  
    println(index)  
}
```
The following is said to be closed range because it will go up to and _including_ the termination value (which, here, is 9). This very well means that the previous code will post 0 through 9 respectfully to your local console.

However, should one hold a wicked prejuidice towards the number 9, it could be eliminated from existence by utilizing the half open range operator. The half open range operator is a tease. It will execute _n _number of times **not**including the final value.

```swift
//Half open range, prints 9 times, or 0 to 8
let count = 9
for index in 0..<count
{
    println(index)
}
```
No matter the road you choose to travel, just be cognizant of the rules. And here, they are dead simple. Make a be greater than b.
```swift
let b = 9
for index in a…b
{
    //A must overcome b
}
```

### For-In Looping
Alas, the majority of your time will be spent using the for-in loop, also branded by the hard streets of programming as iteration. The for-in loop makes its way in most software since it iterates over a given set of items in sequence.

In essence, one iterates over collections in this way.

Due to swift’s strongly typed nature, casting is much less prominent here than Objective-C. This is either a blessing or a curse depending on where you fall on the strong vs weakly typed language argument. Personally, I find it incredibly useful, and more importantly, intentional.

For-in in its purest form:
```swift
let favoriteGames = [“Mass Effect 2”, “Mass Effect”, “Mass Effect 3”]
//Prints the truth
for game in favoriteGames
{
 println(game)
}
```

As you can see, looping over custom or otherwise objects requires almost zero brain function on behalf of the programmer. This is a good thing.
If you, again, are only worried about particular values, feel free to employ the use of the range operators once more:
```swift
let foo = [1,2,3,4,5]
for index in foo[0…2]
{
    println(“First three elements”)
}
```
Bonus points generiously awarded for simple primitive-based collection support (Sorry, NSValue).

If anything, this just displays swift’s flexibility. Also, I dare not break the programmer’s code by not including an arbitrary variable named foo.
So that’s utility in motion, but even better is swift’s iteration over dictionaries returning the friendly tuple. My love for tuples is only further validated by swift’s handling of iteration in the realm of key-value pairs:
```swift
let morgans = [“Jordan”: 26, “Jansyn”: 25, “Bennett”: 1]
// i.e. Jordan is 26 year(s) young.
for (name, age) in morgans
{
    println(“\(name) is \(age) year(s) young.”)
}
```
Discovering what’s in your dictionary in an unordered fashion has, quite literally, never been easier in Cocoa Touch.
What about the need to just loop for the sake of repetition? Swift certainly provides the ability to carry out execution n times. Apple provides an excellent use case, so I will provide it mostly unchanged here:
```swift
//The index is unimportant, just need to execute n times
let base = 3
let power = 10
var answer = 1
for _ in 1…power
{
    answer *= base
}
println(“\(base) to the power of \(power) is \(answer)”)
//3 to the power of 10 is 59049
```
That’s also useful, but alas, I’d be remiss if I left out my personal favorite.
Lest one forget, iterating over characters in Objective-C required it to be Tuesday at noon, the programmer to have a black belt in Krav Maga, and also a net worth of at least 4.3 million.

Swift says screw it — just loop over em’:
```swift
for character in “Hello”
{
    println(character)
}
```
The fact that the previous code sample was elementary should be celebrated. Compare this to Objective-C while you contain your laughter (great sample and explanation via Ole Begemann):
```swift
NSString *s = @”The weather on \U0001F30D is \U0001F31E today.”; 
// The weather on 🌍 is 🌞 today. 
NSRange fullRange = NSMakeRange(0, [s length]); 

//Kill me
[s enumerateSubstringsInRange:fullRange options:NSStringEnumerationByComposedCharacterSequences usingBlock:^(NSString *substring, NSRange substringRange, NSRange enclosingRange, BOOL *stop) { 
    NSLog(@”%@ %@”, substring, NSStringFromRange(substringRange)); 
}];
 ```

 ### Looping Via Function
 Let’s revisit our array of favoriteGames. The for-in loop clearly returns the title at the given index path. But, what if we actually care about that index? You could be quick and dirty:
 ```swift
 let favoriteGames = [“Mass Effect 2”, “Mass Effect”, “Mass Effect 3”]
 var idx = 1
 //Prints the definitive rank of best games and their given rank
 for game in favoriteGames
 {
    println(“\(game) and rank \(idx)”)
    idx++
}
```
But you would never.

Instead, you would use the global function enumerate. Enumerate clocks in every day and dutifully returns an EnumerateSequence struct which contains an index and a value when asked:
```swift
for (rank, title) in enumerate(favoriteGames)
{
    println(“Rank:\(rank + 1) Title:\(title)”)
}
```
This is a respectable way to represent both the index and value in a collection when the occasion calls for such.

### Bonus Points : Looping Via Extensions
You could also use swift’s powerful extension suite to perform loops as well. Did you ever look at the primitive Int and wish the following:
I wish I could execute this code 78 times?
Well, swift is in the business of granting wishes. And here is that particular one come true:
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

78.times
{
 //Prints Hello seventy-eight times 
 println(“Hello”)
}
```
Swift, you’re quite slick. Just don’t ask me what happens if you throw this on a Float and call it 2.5 times.

### Wrapping Up
Swift has quite a few ways one can go about executing code a certain amount of times or otherwise. It’s a testament to its mission — looping in swift is safe, modern, and fast. 

Until next time ✌️.