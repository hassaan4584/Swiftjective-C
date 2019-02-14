---
layout: post
tags: ["Series"]
title: "Random Wisdom: Part 1"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "In our first edition of random wisdom, let's poke around around some Objective-C. Inline blocks, how selectors work and more."
image: /assets/images/logo.png
---
It seems not a week goes by where I stumble upon some code and whisper under my breath, "Objective-C can do that?". Celebrating its 33rd birthday this year, the mature language is the frankenstein of programming — bred by tacking on bits of object-oriented features and Smalltalk style messaging to the already developed C.

As such, there are some interesting techniques due to its inherent amalgamation of ideas, our focus this week.

### Using Inline Blocks within Methods

I came across this technique while viewing [Apple's AstroLayout][1] code (slightly modified for brevity):
```swift
- (NSArray  *)createCompactConstraints {
    // An inline block within a method  
    NSLayoutConstraint *(^createCenterXConstraint)(UIView *, NSString *) = ^(UIView *planetToCenter, NSString *identifierName){
        NSLayoutConstraint *newConstraint =  [planetToCenter.centerXAnchor constraintEqualToAnchor: self.view.centerXAnchor];  
        newConstraint.identifier = identifierName;

        return newConstraint;  
    };
    
    view1Center = createCenterXConstraint(view1, @"view1CenterX");  
    view2Center = createCenterXConstraint(view2, @"view2CenterX");  
    view3Center = createCenterXConstraint(view3, @"view3CenterX");  
    view4Center = createCenterXConstraint(view4, @"view4CenterX");
    
    return @[view1Center, view2Center, view3Center, view4Center];  
}
```

Introduced to most of us in iOS 4, blocks are commonplace in Objective-C and many of the Cocoa Touch frameworks were rewritten to facilitate their power once they were added to the language. Since they enjoy first class citizenship and make capturing state trivial, their utility shines through often.

Despite that, this code originally made me pause. Though its syntax clearly reveals a block, I personally don't see too many defined inside a method. In the seven years I've been Objective-Cing, I can count the times on one hand.

Apple had this to say about the code snip:
```swift
/* Since 8 views are being created that have essentially identical setup code, just use a block to reduce code overhead.

This could be also be done 'longhand' by setting up each planet with its own individual code, or in a separate method outside of this one rather than a block inside. */
```
Personally I find the comment's last bit telling:

> or in a separate method outside of this one rather than a block inside.

We all have our code smells, guidelines and styles — and I can appreciate the to-the-point nature of the code, but I tend toward the triple-S methods (short, sweet and simple).

Even so, would I write the code? Likely not. Is it an interesting display of Objective-C? Yea, I think so.

### objc_msgSend() Fun

The foundation of message passing in Objective-C has many interesting components when one stops to take a look. Largely responsible for Objective-C to partake in duck typing, this bedrock function does a lot.

Whether you adore the fact you can message nil or abhor it, this little guy is one big reason why. Let's peek at its signature:

```swift    
id objc_msgSend(id self, SEL op, ...);
```
Quite simple. A thing can invoke an action — or rather, any number of actions due to its variadic nature. This is already emblematic of the [target-action pattern][2] we use daily. For example, consider this:
```swift
UIView *aView = [[UIView alloc] init];
```
Which could be rewritten with objc_msgSend() as:
```swift   
id aView = ((id (*)(id, SEL, SEL))objc_msgSend)([UIView class], @selector(alloc), @selector(init));

po aView

// In lldb
<UIView: 0x7ffdf2501a30; frame = (0 0; 0 0); transform = [0, 0, 0, 0, 0, 0]; alpha = 0; opaque = NO; layer = (null)>
```

Interesting, no? Further, due to ABI complications ([Hey! We know all about that!][3]) there are different variants of obj_msgSend for floating point return values, structs and more. They aren't much different, signature wise:
```swift
//To get a floating point return value  
long double objc_msgSend_fpret(id self, SEL op, ...);
```
And data structures…
```swift
void objc_msgSend_stret(id self, SEL op, ...);
```

or hopping up to super to get a return value…
```swift
id objc_msgSendSuper(struct objc_super *super, SEL op, ...);
```
However, its similar semantics means it's inherently error prone for daily use by anything outside of clang. That's why we let the compiler handle it, among other things. Further, we enjoy many abstractions on top of obg_msgSend(), which provide us a much saner means to the same end.

Still, it's eye opening to peek inside Objective-C's foundational behavior, and there is plenty of insightful reading on the matter, should you choose to learn more.

### Dereference Object Pointers in LLDB

I had caught this on Twitter via [Jeff Nadeau][4] and found it both practical and intriguing. One can easily deference and object's pointer and have LLDB spit out its content like a struct.

For example, here is the output using the technique in a table view we have in [Buffer][5] (standard vs dereferenced output):
```swift 
po self.tableView

// Console output
<UITableView: 0x7fccf3298600; frame = (0 20; 375 647); clipsToBounds = YES; autoresize = W+H; gestureRecognizers = <NSArray: 0x60000024b5b0>; layer = <CALayer: 0x600000635800>; contentOffset: {0, 20}; contentSize: {0, 0}; adjustedContentInset: {-20, 0, 10, 0}>
```
And then
```swift
p *self.tableView

(UITableView) $3 = {  
    UIScrollView = {  
        UIView = {  
            UIResponder = {  
                NSObject = {  
                    isa = UITableView  
                }  
            }  
            _constraintsExceptingSubviewAutoresizingConstraints = nil  
            _cachedTraitCollection = 0x00006080002e0580  
            _layer = 0x0000600000635800  
            _layerRetained = 0x0000600000635800  
            _enabledGestures = 0  
            _gestureRecognizers = 0x000060000024b5b0 @"5 elements"  
            _window = nil  
            _subviewCache = nil  
            .........  
            //And that's not even half of the output 😲
```

Pertinent, I think, as I've become accustomed to debugging with Swift. Due to its value-type nature, debugging sessions have a different feel than Objective-C. I've found this technique to be applicable in many different scenarios.

### Bonus: Objective-C++

Of course, it's well known that Objective-C walks hand in hand with C++ — thus giving the world Objective-C++. Though its use case can be argued in several contexts, it can be useful for more than just [creating impossibly fast rendering][6].

For example, if you would just as soon skip Objective-C's verboseness altogether with NSDecimalNumber's API, [Objective-C++ could help][7]:
```swift
NSDecimalNumber *operator + (NSDecimalNumber *a, NSDecimalNumber *b) {  
    return [a decimalNumberByAdding:b];  
}
```
Which allows one to author code like this:
```swift    
NSDecimalNumber *addedNums = self.decimalNumOne + self.decimalNumTwo + self.decimalNumThree;
```
Instead of
```swift 
NSDecimalNumber *addedNums = [decimalNumOne decimalNumberByAdding:[decimalNumTwo decimalNumberByAdding:decimalNumThree]];
```
Just remember to tweak you implementation file to ".mm" from ".m" to tell Xcode you're ready to play dirty👌.

### Wrapping Up

Ah Objective-C. You just refuse to die, and I mean that in a good way. You perfectly personify the complete opposite notion of teaching an old dog new tricks — as you are the old dog, who already has a large bag of tricks, in which we developers gleefully discover new ones long since buried along the way.

If anything, the language is a pertinent reminder to stay curious and learning. Mastering our craft is a life long commitment.

Until next time ✌️.

[1]: https://developer.apple.com/library/content/samplecode/AstroLayout/Introduction/Intro.html
[2]: {{ site.url | append:"/architecture-patterns-in-ios-part-1" }}
[3]: https://github.com/apple/swift/blob/master/docs/ABIStabilityManifesto.md
[4]: https://mobile.twitter.com/jnadeau
[5]: https://itunes.apple.com/us/app/buffer-scheduling-for-twitter-instagram-more/id490474324?mt=8
[6]: http://texturegroup.org/
[7]: https://stackoverflow.com/questions/2449422/how-do-i-add-nsdecimalnumbers
