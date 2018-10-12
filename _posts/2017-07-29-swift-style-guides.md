My notes on swift style guides<!--more-->

- Raywenderlich: [https://github.com/raywenderlich/swift-style-guide](https://github.com/raywenderlich/swift-style-guide)

- linkedin: [https://github.com/linkedin/swift-style-guide ](https://github.com/linkedin/swift-style-guide )

- Eure: [https://github.com/eure/swift-style-guide](https://github.com/linkedin/swift-style-guide)

- Github: [https://github.com/github/swift-style-guide](https://github.com/github/swift-style-guide)

- Apples official swift API style guide:  [https://swift.org/documentation/api-design-guidelines/](https://swift.org/documentation/api-design-guidelines/)


### Comment style:
```swift
class Human {
    /**
     This method feeds a certain food to a person.
     - parameter food: The food you want to be eaten.
     - parameter person: The person who should eat the food.
     - returns: True if the food was eaten by the person; false otherwise.
     - throws: see DecodingError
    */
    func feed(_ food: Food, to person: Human) -> Bool {
        // ...
    }
}
```

### Mentioning classes, variables etc:
Use code ticks
/**
 This does something with a `UIViewController`, perchance.
 - warning: Make sure that `someValue` is `true` before running this function.
 */
func myFunction() {
    /* ... */
}

### Expressive comment style
If the code is complicated or unusual etc:
```swift
   /**
    ## Feature Support

    This class does some awesome things. It supports:

    - Feature 1
    - Feature 2
    - Feature 3

    ## Examples

    Here is an example use case indented by four spaces because that indicates a
    code block:

    let myAwesomeThing = MyAwesomeClass()
    myAwesomeThing.makeMoney()

    ## Warnings

    There are some things you should be careful of:

    1. Thing one
    2. Thing two
    3. Thing three
    */
```

### Use mark:

class Pirate {

    // MARK: - instance properties

    private let pirateName: String

    // MARK: - initialization

    init() {
        /* ... */
    }

}
