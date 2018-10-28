---
layout: post
tags: ["Swift"]
title: "Javascript"
author: Jordan Morgan
description: "Javascript and...Swift? It works better than you might think, and for more than just parlor tricks."
image: /assets/images/logo.png
---
Extensions in iOS come in many different forms, but action and share extensions typically lend themselves quite well to being coupled right alongside mobile Safari. And where there is WebKit — there is JavaScript.

I'm not one to venture towards the world's most recognizable scripting language unless I have to — much less without the aid of [friends][2]. Yet, there I was, sitting in my [new office][3] and requiring information from the DOM to complete the task at hand.

And so it is — my quest with iOS and JavaScript began. It turns out that the two opposites get along just fine.

### Callbacks For Days

An action extension's meager beginnings can be traced back to the moment the user invokes them from the share sheet. Then — magic happens.

Specifically — the run method that's found in the identified preprocessor is invoked. Utilizing the current page being handled by Safari — it returns a tidy, wrapped up JSON object to a completion handler. At this point, one can unleash their dirty Swift (or Objective-C) standard library mitts all over it.

Then, the fun.starts();.

### iOS.js

Cupertino & Co.™ typically are associated with black boxing any and every process from the outside world. True though it may be, Mobile Safari comes at developers with a warm smile and welcoming arms.

It all depends on how the developer chooses to implement the JavaScript file. One can access the markup before or after the extension runs and even modify it all together after the extension finishes execution.
```swift 
var GetTXT = function() {};  
GetTXT.prototype = {  
    run: function(arguments) {  
        arguments.completionFunction({ "currentTxt": document.getElementById('txt').textContent});  
    }  
};

var ExtensionPreprocessingJS = new GetTXT;
```

Then, let iOS take over:

```swift 
let extensionItem = extensionContext?.inputItems.first as NSExtensionItem  
let itemProvider = extensionItem.attachments?.first as NSItemProvider

let propertyList = String(kUTTypePropertyList)

if itemProvider.hasItemConformingToTypeIdentifier(propertyList) {  
    itemProvider.loadItemForTypeIdentifier(propertyList, options: nil, completionHandler: { (item, error) -> Void in  
        let dictionary = item as NSDictionary  
        NSOperationQueue.mainQueue().addOperationWithBlock {  
            let results =  dictionary[NSExtensionJavaScriptPreprocessingResultsKey] as NSDictionary  
            let urlString = results["currentTXT"] as? String  
            //Go crazy with DOM elements  
        }  
    })  
}
```

They say there are an infinite amount of possibilities on the web. It's also well documented that there is always an app for "that". So, with JavaScript and iOS getting cozy, I think it's only fair to assume that there are now an infinite of possible apps for anything on any platform, for both this _and_ that. Did I get that right?

### Housekeeping

Of course, iOS only takes you so far. Fortunately, there are only a few steps an aspiring developer must take to mash the front end with Foundation. First, we must think about the actual .js file itself.

The rules here are simple. The developer's responsibility is two fold:

* Create a global object that's named "ExtensionPreprocessingJS"
* Assign a new instance of your custom JavaScript class to that object

This usually looks like our sample a few paragraphs above:
```swift  
var Instance = function() {};  
var ExtensionPreprocessingJS = new Instance;
```

Regardless of which platform one is crafting code for, the custom JavaScript function must define a run() function. Safari will load the supplied .js file as soon as the extension is run, at which point the run() function will be invoked.

At this point, Safari itself will dutifully supply an argument named completionFunction. Lastly, true to the web and restful APIs the world over, one can pass any results that JavaScript can handle in a JSON friendly key-value pair object.

```swift  
var Instance = function() {};  
Instance.prototype = { run:function(args) { args.completionFunction(/*Key-Vals go here*/); }

var ExtensionPreprocessingJS = new Instance;
```

### Completion Functions

One reason I love programming? You can get so cute when you want to. I mean that both metaphorically and literally. And so it is, JSON is to restful APIs the same as cats are to the internet.

Trust me, I am going somewhere with this. I think.

Enter the finalize() function. Safari invokes this function when the extension calls completeRequestReturningItems:Completion: as the very last step in its task. This means anything your extension brought in can be changed any way you want it to.

Which also means that with some hackery, things like this aren't out of the question:

```swift 
var CatDOM = function() {};  
CatDOM.prototype = {  
    run: function(args) {  
        args.completionFunction({"URI": document.baseURI});  
        },  
        finalize: function(args) {  
            document.body.style.backgroundImage = args["cat.png"];  
        }  
    };
var ExtensionPreprocessingJS = new CatDOM;
```

Ah — what a time to be alive!

### Dat .plist doh'

If you've developed any extension, or iOS app for that matter, you're fond of the Info.plist file. This particular file plays an important role for the .js file, as it will happily inform Safari where to look for the JavaScript you want it to run.

Doing so is simple enough. The NSExtensionActivationRule dictionary can contain a key which points to the .js file one has included to the extension target. If the NSExtensionJavaScriptPreprocessingFile key is included, Safari will know to hunt down the file to execute.

### Wrapping Up

They say change is a good thing. I like to fancy myself as someone who might be in a position to vouch for such a claim at this point in my life. As I embark on a new career, I'm learning new things daily and facing new challenges at a blistering pace that have been an adrenaline rush to tackle.

And, it's been insanely fun. So, it only seems fitting that after several years of iOS development — I found myself mixing it with a scripting language I typically try to distance myself from. Something unexpected and new.

So — yes, I think it's true. Change is good. Why not try something new today yourself?

Until next time ✌️.

[1]: http://www.producthunt.com/tech/buffer-for-ios-v5-0
[2]: https://jquery.com
[3]: https://instagram.com/p/8VbqsrKhZw/?taken-by=lookitsjordanmorgan
