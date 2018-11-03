---
layout: post
tags: ["Safari Services"]
title: "SFAuthenticationSession"
author: Jordan Morgan
description: "Single sign on isn't gone, in fact - it's better. Enter a new way to deal with OAuth."
image: /assets/images/logo.png
---
As much as I love open source software, I think an angel gets its wings whenever Cupertino & Friends™ obsoletes a library in any of my projects. The thrill of deleting something in a pod file generally exceeds the initial glee one might experience when finding an existing solution to a complex problem.

So, in memory of all the OAuth libraries out there and Apple spending [10 minutes over OAuth at WWDC 15][1], let's celebrate built in authentication in iOS 11 that takes all of 10 seconds to code — this week's topic.

### Wait but Why

If you've been following safari view controller's brief journey, you may have been one of the many developers scratching their heads when it was announced that in iOS 11 stored data would no longer be shared between its instances.

This was previously a strength of the API, especially with OAuth and single sign on flows that are essentially a core part of any API's DNA. Ad providers that are hellbent on tracking _absolutely everything_ we do online and privacy wins for Apple basically contributed to why we can't have nice things 🙃.

Much like Safari in macOS High Sierra did, so too did safari view controller go to great lengths to enforce protection of the user's data. Its Apple's ethos, which I very much support.

But this time, it won't come at the cost of developer convenience. Better, it may mean you can lessen your code load or drop a third party dependency altogether.

### Hi Auth Session, G'Day [Code Bloat, OAuth Libs, etc]

Thus, we arrive at our new friends doorstep, `SFAuthenticationSession`.

It's nothing more than a simple object used to authenticate a user against a web service (likely OAuth), and (now) unlike safari view controller it'll see and use data from Safari if the user is down with that.

There is certainly beauty in simplicity, and you'll find exactly that here. The API consists of nothing more than a initializer, completion handler type definition, one error code and two methods — appropriately start() and cancel().

The entire API: 
```swift
//Your main entry point  
public init(url URL: URL, callbackURLScheme: String?, completionHandler: @escaping SFAuthenticationSession.CompletionHandler)

//Kick off auth request  
open func start() -> Bool

//Cancel an in flight request  
open func cancel()

//On completion, used in the designated initializer  
public typealias CompletionHandler = (URL?, Error?) -> Swift.Void

//A trivial error that represents a cancellation  
public struct SFAuthenticationError
```

Easily understanding it from nothing more than a glimpse is an accomplishment in of itself, as its [masking quite a workflow][2] under the hood for iOS developers. Further, there's _only_ a few places OAuth is used, as outlined by [OAuthSwift][3]'s readme…

- Twitter
- Flickr
- Github
- Instagram
- Foursquare
- Fitbit
- Withings
- Linkedin
- Dropbox
- Dribbble
- Salesforce
- BitBucket
- GoogleDrive
- Smugmug
- Intuit
- Zaim
- Tumblr
- Slack
- Uber
- Gitter
- Facebook
- Spotify
- Trello
- Buffer
- Goodreads
- Typetalk
- SoundCloud
- Digu
- NounProject

...and more.

### The OAuth Dance

Users on iOS make trips to Twitter, Facebook and everything else in between inside of Safari at some point. It'd be an absolute shame to lose that type of progress inside of our app's own flows — which seemed to be the same case until [beta 3 hit][4].

So lucky for us, this entire post could be summed up with this code sample — which performs the entire OAuth flow:
```swift  
//Typedef block to handle response  
let handler:SFAuthenticationSession.CompletionHandler = { (callBack:URL?, error:Error? ) in  
    guard error == nil, let successURL = callBack else {  
        return  
    }
    
    let oauthToken = NSURLComponents(string: (successURL.absoluteString))?.queryItems?.filter({$0.name == "oauth_token"}).first  
    // Do what you now that you've got the token, or use the callBack URL  
}

//OAuth Provider URL  
let authURL = NSURL(string: "https://api.twitter.com/oauth/authenticate?oauth_token=amazingToken")

//Initialize auth session  
authSession = SFAuthenticationSession(url: authURL, callbackURLScheme: nil, completionHandler: handler)

//Kick it off  
authSession?.start()
```
And just like that, we've

1. Performed an entire OAuth trip with a laughable amount of code
2. Not concerned ourselves with the implementation details of OAuth's spec
3. Stayed entirely within UIKit, and mitigated any need for an outside library
4. …while not having to present any safari view controller ourselves, configure it or do anything else that deals with the view controller lifecycle
5. …and hopefully smiled if you tested this code on your phone with an OAuth provider you've previously signed in with. As you'll see from my colleague Andy's tweet — magic happens:

{% twitter https://twitter.com/ay8s/status/885230327441915904 %}

Of stylistic note, I typically prefer spelling out typedef blocks as an instance variable. Call it an old habit from writing 34 letter methods in Objective-C — the longevity must've stuck. Though, the modern Swift developer will likely opt for a trailing closure instead:
```swift
authSession = SFAuthenticationSession(url: authURL, callbackURLScheme: nil, { (callBack:URL?, error:Error? ) in  
    //Handle auth  
}
```
### Closer Look

While simple and elegant to use, there are some useful things one can glean from its documentation. One thing that evade first time users is scope.
```swift
func authUser()   
{  
    let authSession = SFAuthenticationSession.....  
    authSession.start()  
}
```
This code will produce an alert controller that'll quickly dismiss as the auth session variable is tossed off the stack once the authUser() function returns. Instead, opt for a stronger lifecycle via a property:
```swift
func authUser()   
{  
    //'self' is unnecessary, but illustrates it's an iVar      
    self.authSession = SFAuthenticationSession.....  
    self.authSession.start()  
}
```
Further, the callback URL can be played a few different ways. It's a common flow to thread through to your application delegate:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool   
{  
    //Handle oauth response  
}
```

`SFAuthenticationSession` supports the callback URL to be provided two different ways. The first, and more traditional method, is by registering the custom scheme within the info.plist.

This is the URL that would've been used in our very first code sample — or put simply, by providing nil for the callback URL parameter in its initializer. The second method allows you to forgo this by providing it at runtime:
```swift
//Use plist  
authSession = SFAuthenticationSession(url: authURL, callbackURLScheme: nil, { (callBack:URL?, error:Error? ) in  
    //Handle auth  
}


//Use callback defined at runtime  
authSession = SFAuthenticationSession(url: authURL, callbackURLScheme: myCallbackURL, { (callBack:URL?, error:Error? ) in  
    //Handle auth  
}
```
It's also useful to take note of start(), as may have noticed that it returns a bool:

```swift
let didStart = authSession.start()
```
The session may fail to fire under a few circumstances. Each instance can only kick off _once_, which also intrinsically means if one were to invoke cancel() this would be false as well.

Code like this would be a problem

```swift
authSession = SFAuthenticationSession(... //Setup with initial provider  
authSession.start() //Returns true

authSession = SFAuthenticationSession(... //Setup with another provider  
authSession.start() //Returns false
```
Think of it as a nice glass of scotch 🥃, you swig it done once and you move on with your day (or night, more likely?). If you want more, you make another.

Lastly, it's worth noting that SFAuthenticationSession conforms to Equatable, Hashable and CVarArg. So if you're chomping at the bit to compare them, hash em' up or pass them around in C functions with variadic parameter signatures — feel free to celebrate.

### Wrapping Up

What a little emotional rollercoaster Apple gave us free passes to. They cold clocked us by taking away one of safari view controller's best parts, and then led us to believe that was it. Apple gon' Apple.

But then they hit us back with a mea culpa on beta 3, dropping an API that makes OAuth easier on us and effortless for the end user. Bravo.

Until next time ✌️.

[1]: https://twitter.com/rmondello/status/884508720516014081
[2]: https://tools.ietf.org/html/rfc6749
[3]: https://github.com/OAuthSwift/OAuthSwift
[4]: https://twitter.com/rmondello/status/884458464470343682