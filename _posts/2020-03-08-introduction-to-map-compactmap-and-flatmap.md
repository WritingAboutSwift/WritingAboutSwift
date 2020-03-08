---
title: Introduction to Map, CompactMap, and FlatMap
tags: functional-programming, swift
layout: article
author: dylan
show_author_profile: true
---

If you're familiar with Swift, you've probably heard of them: `map`, `compactMap`, and `flatMap`. These functions make it easy to apply an operation to every element in a `Collection`. But, if Swift or some other functional language wasn't your first, your instinct might be to take the more verbose, iterative approach to solving the same problem.

This article will introduce these higher-order functions.

### Map

`map` applies a closure to every element in a `Collection` and returns the resulting `Collection`. This can be useful when, for example, we want to transform everything in an array.

To illustrate, imagine we have an array of strings that we need to be in all uppercase. The iterative approach is to use a `for` loop to, element by element, create an uppercased version of each string and append it to a new array, like so:

```swift
let exclamations = ["warning!", "caution!", "shit!"]

var uppercased: [String] = []

for exclamation in exclamations {
    uppercased.append(exclamation.uppercased())
}

// uppercased = ["WARNING!", "CAUTION!", "SHIT!"]
```

This works, but it's not terribly elegant. We have to first create an empty variable for our new array and then, step-by-step, append the uppercased versions to the array.

Here's the same code, using `map`:

```swift
let exclamations = ["warning!", "caution!", "shit!"]

let uppercased = exclamations.map { (exclamation) -> String in 
    exclamations.uppercased()
}

// uppercased = ["WARNING!", "CAUTION!", "SHIT!"]
```

A bit cleaner. So what's happening here? `map` will take our closure and apply it to every element in the collection, and return the resulting collection. In this case, it goes through every element in `exclamations` and passes it in as the parameter `exclamation` to our closure, which then runs `.uppercased()` on it.

But we can simplify this even further. Those familiar with closures will know that we can access the first parameter with shorthand using `$0`.

So it can be written as:

```swift
let exclamations = ["warning!", "caution!", "shit!"]
let uppercased = exclamations.map { $0.uppercased }
// uppercased = ["WARNING!", "CAUTION!", "SHIT!"]
```

Much cleaner.

What happens if the thing we're trying to do in the closure sometimes returns `nil`? In this case, `map` will return an array of optional values, some of which may be `nil`:

```swift
let numberStrings = ["1", "2", "three"]
let integers = numberStrings.map { Int($0) }
// integers = [Optional(1), Optional(2), nil]
```

Being returned an array of optionals isn't always desirable. In many cases, you may only want the values that succeeded the transformation. This is where `compactMap` comes in...

### CompactMap

`compactMap()` is just like map, except that it will only include elements for which the closure produced a non-`nil` result. Put differently, it will try to apply the closure to each element and if it succeeds, it will add the unwrapped value to the result, and if it fails (produces `nil`), it won't add the value to the result. This makes more sense with an example. Here's the same code from above, using `compactMap()` instead of `map()`.

Let's first look at how we could do this with a for loop:

```swift
let numberStrings = ["1", "2", "three"]

var integers: [Int] = []
for num in numberStrings {
    if let integer = Int(num) {
        integers.append(integer)
    }
}
// integers = [1, 2]
```

And the more concise `compactMap` version:

```swift
let numberStrings = ["1", "2", "three"]
let integers = numberStrings.compactMap { Int($0) }
// integers = [1, 2]
```

This saves us a lot of code and is more readable.

`compactMap()` can also be used to easily remove `nil` values from an array of optionals:
```swift
let numbers = [1, 2, nil, 4, nil, 6]
let nonNil = numbers.compactMap { $0 }
// nonNil = [1, 2, 4, 6]
```

### FlatMap

And finally, `flatMap()` will "flatten" nested sequences. So if it's passed an array of arrays, for example, it will flatten these out into a single-dimensional array:

```swift
let caliRappers = ["Snoop Dogg", "Kendrick Lamar"]
let chicagoRappers = ["Kanye West", "Chance The Rapper"]
let newYorkRappers = ["Nas", "Notorious B.I.G."]
let allRappers = [caliRappers, chicagoRappers, newYorkRappers]
// allRappers = [["Snoop Dogg", "Kendrick Lamar"], ["Kanye West", "Chance The Rapper"], ["Nas", "Notorious B.I.G."]]

let allRappersFlattened = allRappers.flatMap { $0 }
// allRappersFlattened = ["Snoop Dogg", "Kendrick Lamar", "Kanye West", "Chance The Rapper", "Nas", "Notorious B.I.G."]
```

These map functions offer a more concise alternative to manual iteration when transforming collections of data. Not only are they easier to use, they may also be [faster than using a for loop](https://stackoverflow.com/a/51140310/924025), though your mileage may vary. 