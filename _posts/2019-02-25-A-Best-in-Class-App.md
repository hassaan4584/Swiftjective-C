---
layout: post
tags: ["The Indie Dev Diaries"]
title: "A Best in Class iOS App"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "What is a best in class iOS app? How are they built, and can we quantify what makes them great?"
image: /assets/images/logo.png
updated: "2020-05-25"
special: "true"
---

For as long as I've been a part of this industry, I've watched incredible, well-deserving apps take home an Apple Design Award. And _that's_ my endgame. 

So, years ago, I set out to create a list that succinctly answers only one question:

**What things can I quantify that help make an app great?**

I believe I've created such a list that helps answer that question. Yours might look different, but this one is mine. It attempts to takes all of the emotion and (mostly) opinions out of it. I want to capture what Apple says is great, not what other people may define it as. Though those opinions can assuredly be of value, they don't give out A.D.A.s - only Apple does.

Here's a quick brief on my methodology behind how I created it:

- First, and most importantly, I read iOS' Human Interface Guidelines annually from top to bottom after the GM releases of the upcoming version of iOS. This list applies to primarily the latest version of iOS and iPadOS.
-  Where there's smoke, there's fire. If Apple has said on record that "This is great" about an app, I include whatever that thing or interaction was. These things aren't clandestine trade secrets, but rather they are things that Apple or an Apple design evangelist publicly gave their seal of approval to.
  
That's it. 

> As always, if you've got an item or two that belongs on this list, by all means create a pull request to get it added in by visiting the link at the bottom of the post.

The five sections it covers are:

1. **Accessibility:** Designing for everyone is the right thing to do, and the best apps do it and they do it exceptionally well.
2. **Platform Technology:** Apple loves it when apps utilize their new APIs to great effect, you should too. It's not about shoehorning features, it's about looking at your product and seeing how to utilize iOS around it.
3. **User Experience:** Don't make people think. Your app should have a core function that acts a thesis to a paper - and your UX is the body that supports it.
4. **Design:** Explaining design is hard, but you know a good one when you see it. This section lists some things those apps which are thoughtfully created do.
5. **App Store Presence** This is by far the most nascent category I've been tracking, so its list is short. It includes best practices for the App Store.

### Accessibility is First Class
- Voice Over is fully supported and the rotor control is implemented by including the relevant headings. Using Screen Curtain yields an experience that's not only usable, but up to par with the regular app using only Voice Over.
- [Voice Over gestures][2] are overriden where necessary:
    + **[Escape][3]**: A two-finger Z-shaped gesture that dismisses a modal dialog, or goes back one level in a navigation hierarchy.
        * `func accessibilityPerformEscape() -> Bool`
    + **[Magic Tap][4]**: A two-finger double-tap that performs the most-intended action.
        * `func accessibilityPerformMagicTap() -> Bool`
    + **[Three-Finger Scroll][5]**: A three-finger swipe that scrolls content vertically or horizontally.
        * `func accessibilityScroll(_ direction: UIAccessibilityScrollDirection) -> Bool`
    + **[Increment][6]**: A one-finger swipe up that increments a value in an element.
        * `func accessibilityIncrement()`
    + **[Decrement][7]**: A one-finger swipe down that decrements a value in an element.
        * `func accessibilityDecrement()`
- [Voice Control][1] is also supported.
- Your app respects the following settings:
    + Including Bold Text.
    + High Contrast Cursors.
    + Reduce Transparency.
    + Dark Mode.
    + Reducing Motion.
- When presenting new controllers, you set the Voice Over Cursor to an [appropriate element][8] if the top-most left element doesn't make sense:
    `UIAccessibilityPostNotification(.screenChangedNotification, myHeading);`
- Context considering, you use `accessibilityIgnoresInvertColors` for images and video.
- Adaptive and supports all devices and multitasking scenarios elegantly.
- Fully supports dynamic type.
- Readable text uses `readableContentGuide`.
- Color blind support and a 7:1 color contrast ratio.
- Smart Invert Color Support and the app responds well to color inversion.
- All bar buttons have their landscapeImagePhone and largeContentSizeImage properties set.
- All glyphs have their accessibility images set (i.e. `adjustsImageSizeForAccessibilityContentSizeCategory`).
- Includes closed captions and audio descriptions, all images and icons have alternative text set.
- Leading and trailing margins are used for constraints to support left to right languages.
- The User Interface appears flawlessly when tested using Double Length Pseudo-languages.
- Using `NSShowNonLocalizedStrings` yields no results.
- If you support drag and drop, [UIAccessibilityLocationDescriptors][9] are all set.
- For modally presented views, there is a clear button available to dismiss it instead of relying solely on a swipe gesture.
- Magic taps are supported for the app's most common functionality.
- It uses `CFBundleSpokenName` if the app's name could potentially be mispronounced by the system (i..e CoolApp23 would be "CoolApp Twenty Three").
- Lastly, running the entire app through Accessibility Inspector produced no warnings and turning on Screen Curtain to navigate the app works flawlessly.

### iOS Technology is Tightly Integrated
- Contextual menus are integrated for long presses, showing previews where appropriate.
- It fully supports dark mode.
- Multiple spaces and scene support for iPadOS.
- Home screen quick action support.
- If you offer a sign in, Sing in with Apple is included.
    + If you don't, offer [password autofill][22]
- Spotlight search and indexing support.
- You have custom `UIPointerInteraction` support if your app needs it, and your interface supports cursor support correctly.
    + You don't force any interaction paradigmn over another (i.e. touch is as viable as pointer or keyboard, and vice-versa).
    + You use the correct content effects consistently (highlight, lift and hover).
- Effective energy management (i.e. supports low power mode and reacts to it)
- Keyboard shortcuts have been added. The app could be used almost, or completely, with solely the use of a keyboard.
- It supports handoff on Mac (if applicable).
- Meaningful extensions are included with the app, whether it's via a share extension, action extension, sticker pack, etc.
- Callback urls are supplied and documented so other apps may integrate with it (x-callback-url)/.
- If you display links, consider using [LPLinkView][10]
- Siri Intent support, when plausible:
    - Also include intent phrases to help coach users
    - Alternate app names are included when appropriate
    - Watchface support
- If it makes sense, document sharing is supported via the file provider.
- Drag and drop has first class support:
    - A fully fleshed out `NSItemProvider` exists for custom objects.
    - Editable controls for data entry should also accept its contents via a drop.
    - This is used for reordering, should the app support it.
    - "If you can drag it, it can make a new window."
    - If data could be copied, moved, inserted or duplicated it should also do so via drag and drop.
- If it makes sense, data can be shared via AirDrop.
- Natural language processing support if necessary.
- All tab bar images are vector .pdf images or have each corresponding size included to ensure they adapt correctly and are vended to accessibility modals properly.
- Any displayed Live Photo will animate when force touched and utilize `PHLivePhotoImageView` for playback.
- Each image also shows their system badge if available (i.e. live photo badges).
- If it makes sense, it supports printing via `UIPrintInteractionController`.
- Has Siri Shortcuts supported or donated.
- Running the Analyze function in Xcode yields no errors, warnings or suggestions.
- There are no calls to `UIGraphicsBeginImageContextWithOptions`, and `UIGraphicsImageRenderer` is used instead.
- [Universal Links][11] are supported, especially if your app's content is available online.
- If you need to secure data, you opt to user [Touch or Face ID][23].

### The User Experience is Top of Mind
- Supports [native][12] "undo" and "redo" actions, typically from shaking.
- The content type of all text views and text fields is included, and the correct keyboard type is used for the current context. The keyboard's language identifier is integrated correctly.
- You support [two finger drag to edit gestures][18] in table and collection views.
- If you've customized the back button image for navigation controller, you've also set a `backIndicatorTransitionMaskImage`.[25]
- If you have searching capabilities, it [supports field tokens][19].
- Handles the keyboard being undocked on the iPad, if views are constrained to it via an `inputAccessoryView`.
- If data is quantifiable while data transfer is occurring, a progress indicator is used over an activity indicator.
- It's localized for all territories it's released in.
- Text tends to not truncate and it *never* clips but rather it's always readable.
- All tappable interface elements are at least 44 by 44 points.
- The entire app binary is under 30 megabytes. (_No source here, this is based off a multitude of data points._)
- Delete actions always are followed by a confirmation prompt.
- If your app stores rich information files like a Keynote presentation, it uses the Quick Look API to preview it.
- [State restoration][13] is implemented via `NSUserActivity`.
- It uses the correct audio settings, if audio can be played at all within it.
- Custom edit options are supported when text or an image is selected, if appropriate.
- The user is provided ample time to form an opinion about your app before you request a rating for it.
- The launch screen is branding free and closely resembles the first screen of the app.
- Before opening a link that could lead to another app in a web view or `SFSafariViewController`, try calling `UIApplication`'s `openURL:` with the [`UIApplicationOpenURLOptionUniversalLinksOnly`][14] option first.
- Table views deselect selected rows in [viewDidAppear][15] when popping back to them.
- If you're loading in data, let the user know the progress of the operation either using [determinate or indeterminate views][20].
- Notifications supply a value for [hiddenPreviewsBodyPlaceholder][16] and a detail view.
- `UITextInputAssistantItem` items are used to support common tasks on iPad that are at home within the shortcuts bar.
- When performing CRUD operations on a table or collection view, you opt to use `performBatchUpdates:` instead of `reloadData`.
- Testing for leaks and freed memory is part of your workflow, as consuming an unnecessary amount of memory and power hampers everyone.
- If you have long running tasks, you keep the UI free and up to date by using [background tasks][17].
- You defer from providing custom gestures towards the edge of the screen. If it's appropriate (such as for viewing media), you override [`preferredScreenEdgesDeferringSystemGestures`][24] as needed.
- If you have user interface elements constrained by the keyboard's frame, you handle situations where it may be undocked, floating or split.
- If you support `PencilKit`, include your own undo and redo buttons in a [compact environment][21].
    + Additionally, ensure the system-wide double tap gesture doesn't modify any content if it's been customized by your app.
- Navigation is clear and foolproof:
    - Modality is used sparingly, and clearly brings them back to where they were when dismissed.
- Your design also considers text and your app's voice or messaging as part of the design and experience, and it stays consistent.
    + You don't mix playful error messages juxatposed with serious ones.
    + iOS technology, or any other technical term, is made for anyone to understand (i.e. you'd use 'Scan the tag' instead of 'Activate NFC reading Session' or similar.).

## The Design Drips with Polish
- Correct system margins are used throughout the app, and no hard coded ones are used (i.e. layoutMarginsGuide, safeAreaLayoutGuide, etc.)
- Haptic feedback is used throughout the system to complement user interactions, and they are not overdone.
    + The right haptics are used at the right time. For example, you use `.selection` for only selection changes instead of something like `.rigid`.
    + For example, they often accompany visual and auditory feedback.
- You utilize SF Symbols for common iOS glyphs and the dedicate ones for system APIs (i.e. the gylphs exclusively for iCloud usage).
- Controller transitions feel natural and fluid. Great examples are Calendar and Photos.
- You opt for vector assets to combat the differing resolutions and avoid any blurry assets.
- Your content is always the focus, and you constantly challenge if that's true throughout the development cycles.
- Particular and specific user experience guidelines are followed:
    - No segment controls are used in toolbars.
    - There are no toolbars and tab bars in the same screen.
    - Destructive actions are the top choice in action sheets.
    - Alerts, if used, ideally have to two choices and titles have no punctuation. They avoid using Yes and No as choices.
    - Any picker’s height is equal to about 5 list values.
    - If a progress indicator is in a bar, it should have the unfolded portion of the track clear. Otherwise, it is colored to denote the amount of work left to do.
    - Network activity indicators are only shown if the network requests lasts a few seconds or more.
    - Switches are exclusively used within a table row.
- You aspire to ship on all of Apple's platforms (iOS iPhone + iPad, watchOS, tvOS and macOS).
- Lastly, your app is "jank" free. You know what this means for you.

## App Store Page and Logistical Assets are Thoughtfully Crafted
- An App Store preview video is used.
- Its keywords and category were carefully researched.
- The app icon follows the golden grid:
    - It likely should include your brand’s primary color as well.

### Wrapping Up
I love checklists. And this is my quality checklist that's constantly evolving. My side project will ship in a state where it meets or exceeds almost every single bullet point. 

Certainly, quality takes time. If you nailed everything on this list, it's because you've been working towards them for years. If you aren't close to meeting the items listed here, no stress (it also means you probably shipped!). Take it piece by piece and work your way down.

And, as a wise honey-loving bear once said, "I get to where I'm going by walking away from where I've been." If your app might lack some polish, better now more than ever to spend some time giving it some.

Until next time ✌️.

[1]: https://developer.apple.com/documentation/objectivec/nsobject/3197989-accessibilityuserinputlabels?language=objc
[2]: https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/SupportingAccessibility.html
[3]: https://developer.apple.com/documentation/objectivec/nsobject/1615091-accessibilityperformescape
[4]: https://developer.apple.com/documentation/objectivec/nsobject/1615137-accessibilityperformmagictap
[5]: https://developer.apple.com/documentation/objectivec/nsobject/1615161-accessibilityscroll
[6]: https://developer.apple.com/documentation/objectivec/nsobject/1615076-accessibilityincrement
[7]: https://developer.apple.com/documentation/objectivec/nsobject/1615169-accessibilitydecrement
[8]: https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/SupportingAccessibility.html#//apple_ref/doc/uid/TP40007457-CH12-SW1
[9]: (https://developer.apple.com/videos/play/wwdc2018/241/)
[10]: (https://developer.apple.com/documentation/linkpresentation/lplinkview)
[11]: (https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/WebContent.html#//apple_ref/doc/uid/TP40016308-CH8-SW1)
[12]: (https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/UndoArchitecture/UndoArchitecture.html)
[13]: https://developer.apple.com/documentation/uikit/uiviewcontroller/restoring_your_app_s_state
[14]: https://recoursive.com/2019/02/22/preflight_universal_links/
[15]: https://gist.github.com/smileyborg/ec4812c146f575cd006d98d681108ba8
[16]: https://developer.apple.com/documentation/usernotifications/unnotificationcategory/2873736-hiddenpreviewsbodyplaceholder
[17]: https://developer.apple.com/documentation/backgroundtasks/
[18]: https://developer.apple.com/documentation/uikit/uitableviewdelegate/3183943-tableview?language=objc
[19]: https://developer.apple.com/documentation/uikit/uisearchtextfield
[20]: https://developer.apple.com/documentation/uikit/uiprogressview
[21]: https://developer.apple.com/design/human-interface-guidelines/ios/user-interaction/apple-pencil/
[22]: https://developer.apple.com/documentation/security/password_autofill/
[23]: https://developer.apple.com/documentation/localauthentication/labiometrytype
[24]: https://developer.apple.com/documentation/uikit/uiviewcontroller/2887512-preferredscreenedgesdeferringsys
[25]: https://sarunw.com/posts/what-is-backindicatortransitionmaskimage/?utm_campaign=iOS%2BDev%2BWeekly&utm_medium=web&utm_source=iOS%2BDev%2BWeekly%2BIssue%2B460

