My notes on adding closure support to swifts RegEx class<!--more--> 

#### Why do I need RegEx replace with closure support?

Some times you want to apply logic for each match you want to replace or even just modify the match. Say make it uppercased or reverse the string or or. Maybe you want to make a simple chat bot?

#### Code 

```swift
let str = "bad wolf, bad dog, Bad sheep"
let newStr = str.replace(pattern: "\\b([bB]ad)\\b"){
    return $0.isLowerCased ? $0 : $0.lowercased()
}
Swift.print("newStr: " + "\(newStr)")//bad wolf, bad dog, bad sheep
```

#### You can find RegEx replace with closure support 
(Search for RegExp.swift)
[https://github.com/eonist/swift-utils](https://github.com/eonist/swift-utils) 