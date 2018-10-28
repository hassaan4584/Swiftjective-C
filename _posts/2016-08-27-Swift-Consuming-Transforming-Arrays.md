---
layout: post
tags: ["Swift"]
title: "Consuming and Transforming Arrays"
author: Jordan Morgan
description: "The Swift Stand Library offers up several differeny ways to work with arrays. From sorting to sifting, almost every use case is covered efficiently."
image: /assets/images/logo.png
---
For some time now, I've been wanting to write an extremely short blog post that hits on one central idea. [It was supposed to be this one][1], but hey, words. So, here's a snappy look at some of my favorite ways to slice, dice and search Swift arrays, all in one sentence each.

#### Our Specimen
```swift
var numbers = [1,2,3]
```
#### map(transform:T throws -> T)
Map applies a function over each element:
```swift
numbers.map { String($0) } // Returns ["1", "2", "3"]
```
#### flatMap(transform:T throws -> T?)

Same as above, but eliminates optionals:
```swift
numbers.flatMap { Foo($0) } // Suppose Foo returns nil if the number is odd - this would return only non-nil Foo instances
```
#### reduce(initial: T, combine: (T, T) throws -> T))

Reduce combines elements of the array into one value:
```swift
numbers.reduce(0) { curCount, numToAdd in return curCount + numToAdd } // Returns 6 - (0) is the starting value
```
#### filter(includeElement:T throws -> Bool)

Filter only returns elements that pass a given condition:
```swift
numbers.filter { $0 % 2 == 0} //Returns 2
```
#### sort(isOrderedBefore:)

Returns a sorted array if the elements are comparable by default, otherwise a closure returns true if an element is sorted before the other:
```swift
["Zeke", "Bill", "James"].sort() //Returns ["Bill", "James", "Zeke"]
```
or
```swift
employees.sort { $0.name < $1.name } //Would return an array sorted by the Struct/Classes name property
```
#### indexOf(predicate:(T) throws -> Bool)

Returns the first position that passes the provided test:
```swift
numbers.indexOf { $0 > 1} //Returns 1, i.e. the second element
```
#### flatten()

Concatenates the elements of the array:
```swift
Array([[1,2,3], [4,5,6]].flatten()) //Returns [1, 2, 3, 4, 5, 6] — initialized with an Array since flatten actually returns FlattenBidirectionalCollection
```
#### reverse()

Reverses the elements in the array:
```swift
Array(numbers.reverse()) //Returns [3, 2, 1] — initialized with an Array since reverse actually returns ReverseCollection
```
#### split(separator:T)

Returns a new array, in order, where the elements don't contain the separator:
```swift
numbers.split(1) //Returns [2,3]
```
### Wrapping Up

…and done in 311 words 👍

Until next time ✌️.

[1]: {{ site.url | append:"/on-learning-ios"}}