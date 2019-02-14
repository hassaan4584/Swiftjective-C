---
layout: post
tags: ["Foundation"]
title: "NSURLComponents"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Picking apart and composing URLs becomes a more common programming task by the day. Foundation has a class specifically built for such situations."
image: /assets/images/logo.png
---
It’s the little things.

The more you program, the more you begin to appreciate the discovery of helpful little classes by pure happenstance. I am particularly fond of those that solve a problem in a more pointed fashion than how you may have been solving them in the past.

For me, that’s `NSURLComponents`, this week’s topic of interest.

### Preamble
If you've been around Foundation for some time now, you've likely grown familiar with the way a URL will manifest itself: NSURL. With it, we are able to interact in a number of different ways using HTTP protocol.

```swift
let url = NSURL(string: "http://www.dreaminginbinary.co")
```
Like any protocol worth its salt, HTTP defines a structure and certain order of elements it expects to interact with. We can clearly see an example of this in its purest form from the URL above:

* Scheme — http:
* Host — [www.dreaminginbinary.co][1]

There are many components of a URL, and NSURL contains all of them specified in [RCF 1808][2]. So, as far as representing a valid and usable URL, NSURL does a fine job.

There comes a time, though, when a URL might need to be manipulated in some fashion. Say we decided not to visit [www.dreaminginbinary.co,][3] and instead opted for a quick visit to [www.medium.com.][4]

Not an issue, you say, and promptly code the following:
```swift
url.host = "www.medium.com"
```
Unfortunately, the previous code would produce a glorious compile time exception. If you were to visit the header file for NSURL or the docs, you'd quickly see why:
```swift
var host: [String][5]? { get } // Swift  
@property(readonly, copy) NSString *host //Objective-C
```
NSURL only exposes its components in a read-only capacity. NSURLComponents, on the other hand, was created specifically for situations like this. It's members are exposed in a friendly, readwrite fashion.

You will have no learning curve in getting to know NSURLComponents if you've read this far. They can be created in the same fashion as an NSURL:
```swift
let components:NSURLComponents! = NSURLComponents(string: "http://www.dreaminginbinary.co")
```
And like NSURL, if the string supplied results in a malformed URL, nil will be responsibly set as the value.

Assuming our URL was valid, we are able to manipulate its components as needed:

```swift
components.host = "www.medium.com"
```
Its uses are manifested in situations where one could benefit from mutability. Suppose we had to create a URL dynamically, and all of the components needed are housed in a collection.

If we are to task NSURL with the responsiblity of creating such a URL, we'll be left to several pieces of string concatenation — and before swift — string concatenation was slightly bloated and embarrassingly underpowered.

We'd be using the (not missed):
```swift
myURLSoFar = [myURLSoFar stringByAppendingString:theHost];
```
Or, the _far_ less painful swift variant:
```swift
myURLSoFar += theHost
```
Turning to our new friend, however, yields a syntactically pleasant approach to solving the problem:
```swift
let components = NSURLComponents()  
components.scheme = @"https";  
components.user = @"jmorgan";  
components.password = @"notapassword";  
components.host = @"medium.com";  
components.path = @"/stories/drafts.html";  
components.fragment = @"paragraph4";  
let url = components.url;
```
Here, one can set the logical properties on NSURLComponents. When you are finished, just ask it (nicely) for the valid URL. You'll be promptly returned a spec compliant NSURL, complete with percent escaping and other conveniences.

`NSURLComponents` has everything to clearly and concisely express a URL. Let's take a sneak peek at all the components left at your disposal:
```swift
//All typed as string, save for port - which is a NSNumber and queryItems, which is an Array containing NSURLQueryItems

scheme //An invalid scheme results in an exception  
user  
password  
host  
port //Likewise, a negative port also hands out free exceptions.  
path  
query  
fragment  
queryItem
```
One new property added in iOS 8 is the queryItem collection, which contains useful [tuples][6] of all the items contained in the query string.

If we had initialized an instance of `NSURLComponents` like so:
```swift
let comp:NSURLComponents! = NSURLComponents(string: "https://www.dreaminginbinary.co/jordan?isTired=true")
```
Printing out components.queryItem would yield the following:
```swift
Optional([ {name = isTired, value = true}])
```
And if you're an especially astute reader — you'd notice that the query string conveys the message that I am indeed tired. No matter, though it be 2:00 am in Ozark, Missouri — our time with `NSURLComponents` is done for now.

### Wrapping Up

Become a victim of randomly stumbling across helpful classes. `NSURLComponents` was a result of my curious snooping through header files a mere week ago. Who knows what other interesting secrets Foundation holds within its keep?


Until next time ✌️.

[1]: http://www.dreaminginbinary.co
[2]: http://www.freesoft.org/CIE/RFC/1808/
[3]: http://www.dreaminginbinary.co,
[4]: http://www.medium.com.
[5]: https://developer.apple.com/library/etc/redirect/xcode/ios/1048/documentation/General/Reference/SwiftStandardLibraryReference/index.html#//apple_ref/swift/struct/String
[6]: {{ site.url | append:"/swift-tuples" }}