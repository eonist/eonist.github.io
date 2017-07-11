## Examples of singletons

```swift
class SingletonA {
    static let sharedInstance = SingletonA()
    init() {
        println("AAA");
    }
}

class TheOneAndOnlyKraken {
    static let sharedInstance = TheOneAndOnlyKraken()
    private init() {} //This prevents others from using the default '()' initializer for this class.
	func test(){
		print("hello world")
	}
}


let singleton = TheOneAndOnlyKraken.sharedInstance
singleton.test()
```

## Another singleton example: 

```swift
// Creating our Singleton

class SharedManager {
    // Declare our 'sharedInstance' property
    static let sharedInstance = SomeManager()

    // Set an initializer - 
    // it will only be called once
    init() {
        print("SomeManager initialized")
    }

    // Add a test function
    func doSth() {
        print("I'm doing something")
    }
}

// The 'init' function will 
// only be called the first time
SharedManager.sharedInstance.doSth()
```