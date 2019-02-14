---
layout: post
tags: ["Series"]
title: "iOS Architecture Patterns: Part 3"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Our third and final discussion on common iOS archiecture patterns, and perhaps the most important. Let's chat MVC."
image: /assets/images/logo.png
---
I get it. This is iOS 101. If you have a sliver of iOS experience, you could probably just move along. In fact, I almost didn't even write this. But then I realized that would be…careless.

How could I write up a series on iOS architecture and _not_ talk about MVC? If you are reading this and you're relatively new to iOS, take this to heart. Learn it. And, well — I'd love to help with that right now.

So, let's talk about iOS' backbone.

### In the Beginning

Let's skip the dancing and get straight to the point. You'll want to know how MVC works if you want to be able to understand what iOS is trying to do in terms of architecture. From the beginning, Cupertino & Friends**©** crafted iOS to work in MVC terms.

If it's somewhat new to you, if you keep watching [Paul Hegarty's course][1] and still have no clue what he's talking about — I've been there. So, let's go basic, shall we?

MVC aims to break up your software architecture into three, distinct and meaningful parts. The aim is that each file you add, each line you write and each outlet you connect generally falls into one of three buckets.

Ladies and gents, meet your contestants:

* The dashing, data filled **Model**
* The image obsessed, superficial **View**
* And the smooth operator, sometimes overbearing **Controller**

Now that we know the game we're playing, don your imaginary learning hat ([or real one][2]) and pretend we're creating a basic UI that shows a user avatar. That's all it does — show one picture.

### Contestant 1: The Model

Models aren't the glamorous one in the whole MVC deal. They stay behind the scenes and provide important things, but the whole gig falls apart without em'.

If you ask Apple, models are "objects that encapsulate the data specific to an application and define the logic and computation that manipulate and process that data."

If you don't like falling asleep while reading documentation, just remember this: Models have the data that define a piece of your software. They house it and are responsible for it.

So, with our simple screen showing a user avatar — I think it's fair to throw out a few assumptions:

* The application has some notion of a user signing up
* The application only cares about their name and image

Given that, you should subconsciously start forming a model in your head. In practice, this will likely manifest itself as a class or struct
```swift  
class User  
{
    let name:String  
    let avatarImg:UIImage

    init(userName:String, userAvatar:UIImage)  
    {  
        self.name = userName  
        self.avatarImg = userAvatar  
    }  
}
```
What your model shouldn't look like:
```swift
class User  
{
    let name:String  
    let avatarImg:UIImage  
    let containingVC:UIViewController
    
    init(userName:String, userAvatar:UIImage, vc:UIViewController)  
    {  
        self.name = userName  
        self.avatarImg = userAvatar  
        self.containingVC = vc  
    }
    
    func addImageToViewController()  
    {  
        let imgVw = UIImageView(image: self.avatarImg)  
        self.containingVC.view.addSubview(imgVw)  
    }  
}
```
Models hold data. They help communicate changes to that data to the controller, but by definition they shouldn't do things like the above. It's managing the view layer here, which is unbecoming of a model.

Tsk tsk.

### Contestant 2: The View

The view is all about that glitz and glam. It takes all the delicious bytes from the model and displays it in a way users can understand, see or interact with. Your old (or new) friend UIView is king of the block in Cocoa Touch when it comes to views.

With our mock scenario, we'd have a UIImageView. Here, this would play the view role in MVC. It knows nothing about a User, but it can take the User's avatarImg property and display it.

Of note, in Cocoa Touch it's quite common that you might not need to code up your own view. UIKit is stocked to the brim with great turnkey solutions. Here, the interaction with the view may be as simple as this:
```swift  
let profileImg = UIImage(named: "profile")  
let jordan = User(userName: "Jordan", userAvatar: profileImg!)

//Later on, in a controller  
self.profileImageView.image = jordan.avatarImg
```
However, you've taken a wrong turn if it looks something like this:
```swift
class TopImage:UIImageView  
{  
    func getUserImage()  
    {  
        let someSingleton = AppSingleton.sharedInstance()  
        let userImg = someSingleton.currentUser().avatarImg

        if (userImg == nil)  
        {  
            //Look for some other image property on User  
            //Do some API call to tell your DB that the User  
            //Is invalid since it doesn't have a profile image  
            //Or something a tad outlandish  
        }  
        else  
        {  
            self.image = userImg  
        }  
    }  
}
```
Much like the poor model example before, this view is just dipping its toes where it doesn't belong. A view is not where you want networking, model specific behavior or really any heavy lifting.

Views — they are updated from a controller and can let the controller know about user action.

### Contestant 3: The Controller

The controller basks in the glory of being able to control its two minions — the view and model. Really, it isn't a big a deal as some make it out to be. In reality, if you've implemented it right it simply acts as a intermediary layer between the model and view.

In fact, Apple has all but ensured we use them as such. The dead giveaway? The name: UIViewController.

These iOS staples help to control a view — and they take the model's data and feed it to those views. Controllers are also ideal for performing those setup tasks, and even more importantly they'll live out the view lifecycle.

Perhaps a post for another day — but knowing how to use viewDidLoad/appear/etc and understanding them early on will serve you well.

In the circle of life, controllers can update models and also update views. Thus, it becomes the glue that holds MVC together:
```swift
class ProfileViewController:UIViewController  
{  
    //MARK: Properties  
    @IBOutlet weak var profileImage: UIImageView?  
    let currentUser:User
    
    //MARK: Initializers  
    init(curUser:User)  
    {  
        self.currentUser = curUser  
        //Lazy town  
        super.init(nibName: nil, bundle: nil)  
    }
    
    required init?(coder aDecoder: NSCoder)  
    {  
        fatalError("init(coder:) has not been implemented")   
    }
    
    //MARK: View Lifecycle  
    override func viewDidLoad()  
    {  
        self.profileImage?.image = self.currentUser.avatarImg  
    }  
}
```
Controllers should not look like:
```swift 
class ProfileViewController:UIViewController  
{  
    //DOES LITERALLY EVERYTHING (MVC) OR HAS THE LOGIC FOR THE WHOLE  APP BECAUSE MODEL-VIEW-FORGET IT  
}
```
Perhaps the most common folly of controllers (on iOS — specifically the UIViewController) is that way too much logic ends being housed in them.

Since the novice engineer knows that API calls don't fit in views or models — they then assume things like that _must_ go in a controller. It's an understandable pit to fall into, but OOP rules still will serve you well here. Abstract things out that make sense to be housed in a singular, purposeful class.

### Stay Cool

Crafting code that is pristine is an amazing feeling. Sometimes, you can't always do that. If it needs to ship and you can keep things as clean as possible — [I get it][3]. Sometime patterns might take a backseat.

If you are starting out, don't get overwhelmed by all the MVC variants. There seem to be as many proposed lately as their are javascript frameworks (so, millions). Focus on learning MVC first, and then the rest will magically and slowly start to make sense.

### Wrapping Up

These patterns can really help, they are indispensable as guides. However, if you think about a perfect architecture all day long, it's paralysis by analysis. You'll never release squat.

But when you do give thought to how you want to do things — MVC is good stuff. When you go down this route, and really stick to it — things are generally reusable, responsibilities clear and interfaces are clearly outlined. It makes your life easier and the code flows easier.

And hey, I'm betting people a lot smarter than us came up with it. They lived and breathed iOS to bring it to fruition back in 07'. The land of Cocoa Touch is bootstrapped on MVC, so come join the design pattern fun.

Until next time ✌️.

[1]: https://itunes.apple.com/us/course/developing-ios-8-apps-swift/id961180099
[2]: http://news.vanderbilt.edu/2014/03/thinking-cap/
[3]: https://medium.com/swlh/from-idea-to-app-in-45-days-how-we-built-a-mobile-tool-for-our-remote-team-s-retreat-f5e669a78e1f#.o07y269yp