---
layout: post
tags: ["Foundation"]
title: "NSMeasurement"
author: Jordan Morgan
description: "Measuring units can be a daunting task, especially with the global audience we often serve. Let's see how Foundation can lend a hand."
image: /assets/images/logo.png
---
Measuring units, things or items in software won't always be the most glamorous job for software engineers. Though not as thrilling as writing performant user interface libraries nor as fulfilling as conjuring up networking code that weaves concurrent execution with ease, measuring units in iOS is still done more often than not, wrong.

How can one author such code in an internationlized manner and not in a precipitous one? Foundation, as is so often the case, holds the answer. And it has since iOS 10.

This week, let's look at NSMeasurement and friends.

### More Common than You Think

Software has always had situations that crop up which present users with several things that are either measurable or generally quanitifiable. And when that software (iOS) runs on billions of devices across the globe, the need to represent internationalized values of those things becomes not only more important, but quite key.

Getting such measurements and units right isn't a baseline experience, you should just expect it of your software.

So with that- these APIs aid not only just with the obvious "converter" apps, but also with games, shopping lists and everything else in between:
* Are you representing a measurement of time ⌚️?
* A distance traveled 🚙?
* The rate at which we traveled 🗺?
* The weight of an object ⚖️?

As such, the temptation to use a simple double type makes sense on the surface level and simply breaks apart anywhere else:
```swift 
//Technically we even set up the variable name for failure  
let milesTraveled:Double
```
### NSMeasurement and all his Units

Following the previous example, what we really need is an accurate, bonafide construct to represent a measurement. And while miles is certainly a measurement, it's only one that generally makes sense within the context of Murica' 🇺🇸. As such, Foundation's support for a measurement is tactfully generic:
```swift     
public struct Measurement 
```
> Note that within the text, for stylistic purposes and because I code in Objective-C daily, I'll refer to the frameworks with their NS prefix. All of these objects are properly bridged over to Swift as value types and structs.

It provides a way to provide a generic _unit_ of measurement and also a value that corresponds to it. Its initializer will ask for both:
```swift 
public init(value:Double, unit:UnitType)
```
Every unit will always have a symbol, though not every unit has a dimension nor will they always be equivalent to one another. It's something to note, sure, but one will most likely work with dimensional units. But, it's for this reason that NSUnit's designated initializer will require only a symbol:
```swift     
public class Unit: NSObject, NSCopying  
{  
    public let symbol:String  
    public init(symbol: String)  
}
```
When it comes to NSMeasurement, this unit type drives the majority of the work, and you can define which to use via its initializer, but more commonly you'll use an NSDimension (which subclasses NSUnit) provided to us by Apple. Each dimensional unit will then drive down further into a given dimension that exists within the unit.

For example, say we wanted to measure time. Our unit would be duration, but we've got several ways to represent duration such as the second, minute and hour:
```swift 
let abstractValue = 1.0

// 1 second  
let seconds = Measurement(value: abstractValue, unit: UnitDuration.seconds)

// 1 minute  
let minutes = Measurement(value: abstractValue, unit: UnitDuration.minutes)

// 1 hour  
let hours = Measurement(value: abstractValue, unit: UnitDuration.hours)
```
Foundation includes a truckload of dimensional units, everything from electrical currents to pressure. Though you are free to subclass and create your own units, in fact — there is quite robust support for that, I'm not sure you'll ever need to.

Here are some common dimensional units baked in for free:

* UnitLength : Base Unit is meters (m)
* UnitMass : Base unit is kilograms (kg)
* UnitDuration : Base unit is seconds (sec)
* UnitArea : Base unit is square meters (m²)
* UnitAcceleration : Base unit is meters per second squared (m/s²)

There are about 170 dimensional unit types and it's likely Foundation has thought of your use case.

### Operating on Measurements

Working with measurements is trivial due to the fact that they conform to equatable out of the box, so comparisons are carried out uniformly:
```swift   
let abstractValue = 1.0

let seconds = Measurement(value: abstractValue, unit: UnitDuration.seconds)  
let minutes = Measurement(value: abstractValue, unit: UnitDuration.minutes)

// 61.0 seconds, measured in the dimension's base unit  
let totalTime = seconds + minutes

// 30.5 seconds  
let halfTheTime = totalTime/2
```
The entire measurement API does all of the heavy lifting for you. This is true when you operate on units of the same dimension but in different forms. The result of the operation will simply provide the base unit type:
```swift 
let imperialLength = Measurement(value: 5280.0, unit: UnitLength.feet)

let metricLength = Measurement(value: 0.62, unit: UnitLength.kilometers)

// 2229.344 meters  
let totalLength = imperialLength + metricLength
```
Extending the usefulness is NSUnitConverter, which is an abstract class that converts a unit to and from the base unit of its given dimension. For most cases, that's going to be units that adhere to a linear equation or a scale factor. As such, UnitConverterLinear will be supplied:
```swift 
let imperialLength = Measurement(value: 5280.0, unit: UnitLength.feet)

let metricLength = Measurement(value: 0.62, unit: UnitLength.kilometers)

// 2229.344 meters  
let totalLength = imperialLength + metricLength

// 1.385 miles  
let justMiles = totalLength.converted(to: UnitLength.miles)
```
Don't worry about creating conversions that don't relate to one another. These will produce build time errors due to each type's conversion requiring its generic unit type:
```swift 
// Build error  
let nonsense = totalLength.converted(to: UnitTemperature.celsius)
```
### User Facing Values

Making these values show up in your user interface is going to be quite familiar if you've ventured into NSNumberFormatter's waters. Its close cousin, NSMeasurementFormatter, use is essentially identical.

This is quite ideal, as writing these types of strings on our own time would quickly become a chore.
```swift     
if (isCanada)  
{  
    // kilometers 👌  
}  
else if (isChinese)  
{  
    // Translate the unit 😐  
}  
else if (isArabic)  
{  
    // Translate the unit, number representation AND make it right to left 😱  
}
```
Of course, Foundation and friends nails it:
```swift 
let distance = Measurement(value:10, unit: UnitLength.miles)

let frenchDistance = MeasurementFormatter()  
frenchDistance.locale = Locale(identifier: "fr")

let chineseDistance = MeasurementFormatter()  
chineseDistance.locale = Locale(identifier: "zh")

let arabicDistance = MeasurementFormatter()  
arabicDistance.locale = Locale(identifier: "ar")

// 🇫🇷** **-> 16,093 km  
print("🇫🇷 -> (frenchDistance.string(from: distance))")

// 🇨🇳** **-> 16.093公里  
print("🇨🇳 -> (chineseDistance.string(from: distance))")

// 🇯🇴** **-> ١٦٫٠٩٣** كم**  
print("🇯🇴 -> (arabicDistance.string(from: distance))")
```
Since measurements tie in closely with numbers, it's also possible to pair a measurement formatter with a number formatter to specify digits, for example:
```swift 
let distance = Measurement(value:0.2, unit: UnitLength.miles)

let frenchDistance = MeasurementFormatter()  
frenchDistance.locale = Locale(identifier: "fr")

// 🇫🇷** **-> 0,322 km  
print("🇫🇷 -> (frenchDistance.string(from: distance))")

let digitFormat = NumberFormatter()  
digitFormat.minimumSignificantDigits = 4

frenchDistance.numberFormatter = digitFormat

// 🇫🇷** **-> 0.321868 km  
print("🇫🇷 -> (frenchDistance.string(from: distance))")
```
There are several configurations that the measurement formatter affords to you. Be sure to glance over its [documentation][1]. Also note that the formatter will takes its default system locale when initialized, so it's typically unnecessary to directly assign to its locale property as we've done here.

### Testing Locales — The .easy Way

A quick foot note. Part of the magic of utilizing Foundation's measurement and unit APIs is that they are locale aware. If you're currently changing the location within the iOS simualtor to see how things shake out, there is another way you might prefer.

Just dupe your scheme, and pick the desired locale:

* Edit Scheme
* Hit "Duplicate Scheme", it's in the bottom left in Xcode 9
* Name it
* Under Run -> Options -> Application Region, then choose the region to test with

This is nice, because it's a no fuss and deliberate approach. It requires no code changes or mucking around with the (always reliable 🤞) iOS simulator.

Additionally, you can use the same approach to test string localizations within your interface by editing the "Application Language" in the same manner you edited the region.

### Wrapping Up

It wasn't until I started working on an international team that I truly began to appreciate accurate measurements within iOS. Or — even the correct unit of measurement _period_.

While the rest of the world embraces the metric system, here I am sticking out like a sore thumb while communicating distances via the imperial system. Siri has a soul, and I know this because she is sick of answering how many miles equals 1 kilometer. I hear it in her cold, hard, digital voice when she answers it for 144th time for me 🤖.

Let's all aspire to not be that app that delivers the wrong units, or incorrect measurements, in our own software. Foundation has us covered.

Until next time ✌️.

[1]: https://developer.apple.com/documentation/foundation/measurementformatter

