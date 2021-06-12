My notes on swizzling in swift<!--more-->.

### Basic swizzling:
Swizzling is all about replacing the static "variable method" with another static variable method. Bare in mind that all instances of that class also gets this new method variable

```swift
import Cocoa

// Only needed until we have class variables
var __SwizzleSayHello = { (who:String) -> String in
    return "Hello, \(who)"
}

class Swizzle {
    //Only needed until we have class variables
    class var _sayHello : (String) -> String { get{ return __SwizzleSayHello } set (swizzle) {__SwizzleSayHello = swizzle} }

    func sayHello(who: String) -> String{
        return Swizzle._sayHello(who)
    }
}

let immutableInstance = Swizzle()
var mutableInstance = Swizzle()

//Both print "Hello, World"
print(immutableInstance.sayHello("World"))
print(mutableInstance.sayHello("World"))

Swizzle._sayHello = { (who:String) -> String in
    return "Howdy, \(who)"
}

//Both print "Howdy, World"
print(immutableInstance.sayHello("World"))
print(mutableInstance.sayHello("World"))
```
