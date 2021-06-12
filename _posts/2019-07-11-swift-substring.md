My notes on substring<!--more-->Swift have tedious string methods. These are common programming string modifiers and parsers:

## Beginning of a string
```swift
let index = str.index(str.startIndex, offsetBy: 5)
let mySubstring = str[..<index] // Hello
```

## Prefix:
```swift
let index = str.index(str.startIndex, offsetBy: 5)
let mySubstring = str.prefix(upTo: index) // Hello
let mySubstring = str.prefix(5) // Hello
```

## End of a string subscripts:
```swift
let index = str.index(str.endIndex, offsetBy: -10)
let mySubstring = str[index...] // playground
```

## Suffix:
```swift
let index = str.index(str.endIndex, offsetBy: -10)
let mySubstring = str.suffix(from: index) // playground
let mySubstring = str.suffix(10) // playground
```

## Range in a string
```swift
let start: String.Index = str.index(str.startIndex, offsetBy: 7)
let end: String.Index = str.index(str.endIndex, offsetBy: -6)
let range: Range<String.Index> = start..<end

let mySubstring = str[range]  // play
```
