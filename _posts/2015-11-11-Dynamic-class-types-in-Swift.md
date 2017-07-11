Here is a pure swift implementation of dynamic class types. It does require the classes to extend the same protocol.<!--more--> 

```swift
protocol ILayout{ init(_ a:String)}
class A:ILayout{required init(_ a:String)}
class B:ILayout{required init(_ a:String)}
var instance:ILayout
var classType:ILayout.Type

classType = A.self
instance = classType.init("abc")

classType = B.self
instance = classType.init("abc")
```