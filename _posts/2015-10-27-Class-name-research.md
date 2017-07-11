**Obtaining a Class-Name of a custom class in swift**:<!--more--> 
```swift
var className: String? {
    return __FILE__.lastPathComponent.stringByDeletingPathExtension
}
//Now you can simply call the class name on your object like so:

myObject.className
//Please note that this will only work if your class definition is made within a file that is named exactly like the class you want the name of.

//As this is commonly the case the above answer should do it for most cases. But in some special cases you might need to figure out a different solution.

//If you need the class name within the class (file) itself you can simply use this line:

let className = __FILE__.lastPathComponent.stringByDeletingPathExtension
```

**Better solution for custom class name in swift:**
```swift
//To get class name as String declare your class as following
@objc(YourClassName) class YourClassName{}
//And get class name using following syntax
NSStringFromClass(YourClassName)
```

Some great stuff here related to Element style selectors (Specificity of  cascade ordering):
```
http://nshipster.com/swift-comparison-protocols/
http://www.w3.org/TR/CSS2/cascade.html#cascading-order
```
