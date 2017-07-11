<!--more--> 

```swift
protocol Decoratable{}
class A:Decoratable{}
class B:Decoratable{}
let object:AnyObject = A()
object.dynamicType is A.Type//true
object.dynamicType is B.Type//false
object.dynamicType is Decoratable.Type//true
```
**NOTE:** Notice that it also works with protocols the object may or may not extend

**CAUTION:** If an object is an optional it wont work unless you "unwrap" it with the "!" char

**UPDATE 1:**
There is now an article named "==Asserting class and protocol==". Which elegantly compares class and protocol types with generics. 