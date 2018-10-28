---
layout: post
tags: ["Misc"]
title: "simctl"
author: Jordan Morgan
description: "The iOS emulator is deceptively powerful. Learn about its C.L.I. to automate all sorts of things."
image: /assets/images/logo.png
---
I remember the day I first ran a command in Terminal. At that moment, I truly felt like a bonafide programmer. With my fellow students huddled around my laptop at college, I swiftly pecked out a command with steadfast confidence while acting as if I'd done it a million times before.

For the modern iOS developer, spending time in Terminal and firing up the shell of choice on macOS, Bash, is commonplace. Today, we'll look at a utility one can use in Terminal that's been around for a few years but has largely eluded the spotlight:

**simctl** — a lovely little tool to aid with the iOS simulator.

### Care Package

So, what is it? First released alongside Xcode 6, simctl is a command-line utility (itself housed within xcrun) that can be used in Terminal to do all sorts of helpful things with the iOS simulator. It supports simple tasks such as device book keeping to more immediate time savers such as recording video.

This simple C.L.I. wrapper essentially parlays the power of Xcode's tools over to Terminal. Though they are available for individual download, in the modern era we simply have them included as part of the Xcode package.

### Betas Gonna Beta

With iOS 11 knocking at our doorstep, and Xcode 9 currently fending off bugs — if you're playing along today, I find it incumbent to ensure you're using Xcode 8.3.3's tools:

```swift
xcode-select --print-path  
# If that doesn't include Xcode-Beta in it, all clear
```
With most development environments, that should produce something like this:
```swift
/Applications/Xcode.app/Contents/Developer
```
If you currently using Xcode 9's tools, making a switch is trivial:
```swift
sudo xcode-select -switch (the path to Xcode)/Xcode.app
```
That said, let's peek at what simctl can bring to the table.

### Less Quicktime, More Terminal

One of my favorite uses of simctl is to capture a quick video from the iOS simulator. If you're devin' on some new features and want to put it out on the Twittersphere, or simply need a visual aid to show a product owner or Q.A. — one command can get you there:
```swift
xcrun simctl io booted recordVideo (filename).(extension)
```
When used, a high fidelity recording begins capturing everything on the main framebuffer display of the simulator. When you're ready to stop, simply return to Terminal and enter CTRL+C.

Feel free to save files as either a .mov, .h264, .mp4 or .fmp4. The opportunity is there to think outside the box, too. Take, for instance, the ability to replace a file destination with a URL to establish a server socket to pipe video over 💯.

The equivalent does exist for screenshots as well, with built in support for .png, .tiff, .bmp, .gif and .jpeg:

```swift
xcrun simctl io booted screenshot myScreenShot.png
```
But, hey — CMD+S it just a bit easier sooooo ¯\\_(ツ)_/¯.

### Device Selection

The perceptive reader may have noticed the "booted" argument used in the previous commands. All of simctl's sub commands have a device parameter, and each device ran via the simulator has its own UDID. To view these yourself, feel free to employ — what else — a simctl command:

```swift
xcrun simctl list
```
This produces a comprehensive list of of device pairs, types, their availability and runtimes available for the simulator to use. On mine, I can clearly see the iPhone 5 I've got currently running among the output:

```swift
iPhone 5 (D1F67F00-FA3D-42B7-9E2F-FEF23809D4A0) (Booted)
```
That means I could just as well take a screenshot with simctl by supplying its UDID for the device parameter:

```swift
xcrun simctl io D1F67F00-FA3D-42B7-9E2F-FEF23809D4A0 screenshot screen.png
```
As you've likely concluded, passing "booted" for the device argument automatically supplies the booted device's UDID on our behalf. If there is more than one device running, simctl will just pick one of them.

For the remainder of this article, I'll use booted where the device parameter is expected.

### iOS Developer, and Doctor

Let's face it, nobody likes log files. Developers ask for them, consumers have no idea what they are and when we do use them they are usually bereft of useful formatting or digestible text.

But yet, as developers _we just can't stop #loggingallthethings_. Usually for good reason, too. That said, I'd be remiss if I didn't mention you can generate an incredibly detailed log of anything that's happened in your simulator session with simctl:
```swift
xcrun simctl diagnose
```
After you move past the wall of text warning you that the dump contains *only* your personal information, iCloud Accounts, Apple ID, name, user name, email address and settings, file paths, downloads, IP addresses, network connection information and installed applications — what you find has some neat stuff in it:
* System Logs
* Simulator Logs
* Device and Environment .plist settings
* And a whole load of more information

But, yes — buyer beware. Keep that log stashed locally unless the idea of a total stranger standing over your shoulder while you use a MacBook doesn't frighten you at all.

### URL Schemes

Another increasingly common part of iOS development is testing url schemes. With deep linking becoming a powerful tool to incorporate both as a consumer and a provider, simctl can help here too:

```swift
xcrun simctl openurl booted https://www.dreaminginbinary.co/
```
And just like that, the active device pops open Safari with the URL. Of course, if you know other [app's schemes][1] — those are fair game, too:

```swift
xcrun simctl openurl booted sms: #Open Messages
```
Though that previous commands simply opens messages, parameters are fully supported as well. For example, if you wanted to send yours truly a text message — fire away:

```swift
xcrun simctl openurl booted sms:1-417-323-2345
```
This demonstrates two things

* The flexibility of simctl
* And a fake phone number 😉

### Odds and Ends

Though simctl has a robust toolset that's worth your time, here are some other quick hitters of some of my personal highlights:

**_Adding Media_**

```swift
xcrun simctl addmedia booted (path to file, or files)
```

**_Printing Environment Variables_**
```swift
xcrun simctl getenv booted (variable name)
```

**_Forcing an iCloud Sync_**
```swift
xcrun simctl icloud_sync booted
```
**_Reset Device Content and Settings_**
```
xcrun simctl erase booted
```
**_Install an App On Device_**

```swift
xcrun simctl install booted (The path to the app)
```
**_…And Launching Apps (via bundle ID)_**

```swift
xcrun simctl launch booted (ID)
```
There is quite a lot of pieces here for us to mash together to pull off some truly capable workflows. You can even create your own custom simulator, as Erica Sadun outlines in detail [here][2]. Be sure to study the whole list of things it can do for you:

```swift
xcrun simctl -help
```
### Short and Sweet

While not directly related to simctl, it's worth noting that you can employ some syntactical sugar to simctl's commands, if you will, in the form of an alias. For the uninitiated, this allows one to circumvent longer commands in lieu of a shorter, more personalized one:

```swift
xcrun simctl list #List all simulator devices
```
can become something like

```swift
simDevices
```
Doing so requires just a hop, skip and step around your local bash profile. Dropping an alias definition here will ensure one can use it time and time again between terminal sessions. Otherwise, alias definitions are scoped to the current terminal process:

```swift
nano ~/.bash_profile #Or any other text editor...

#For example, with Xcode: 
open -a Xcode ~/.bash_profile
```
Then, simply define your alias and save it. In the beloved Nano, this is simply executed as such:

```swift
alias simDevices="xcrun simctl list"

# Hit CMD+O to save  
# Then CMD+X to exit  
# Profit from shorter commands like a boss
```
Of note, Bash alias' don't have the luxury of accepting parameters. If you yearn to record a video with simctl as previously mentioned, but with the allure of shorter commands — one would need to define a function instead. In this case, it would facilitate the parameterization of the recording's file name.

### Wrapping Up

While command line utilities aren't the heroes of iOS development, it cannot be argued that they are indeed useful. Bolstering our tool belt as software engineers cannot be understated. Much like buying insurance, it's not so incredible day in and day out — until the day comes when you really need it.

Now, if you'll excuse me, I'm off to…
```swift
xcrun simctl openurl N789YE9Q-3TSV-9083-B314-A3NBS64GOP90 http://bit.ly/weekend_planz
```

Until next time ✌️.

[1]: https://developer.apple.com/library/content/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html
[2]: http://ericasadun.com/2014/06/18/ios-8-building-custom-simulators/