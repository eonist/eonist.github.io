**NOTE:** it does require that each class extends the same protocol and that they have similar init method<!--more-->   
**NOTE:** required prefix in the init is so that instances can be created via factory design patterns
```swift
class A:IA{required init(){}}
protocol IA{init()}
func test(classType:IA.Type){
    let instance:IA = classType.init()
    Swift.print("instance: " + "\(instance)")
}
test(A().dynamicType)//instance: A
test(A.self)//instance: A
```


Here is how the dynamicType variable behaves when subclassing:
  
```swift
//DynamicType test: If you use the dynamicType call, you will get the Subclass
//To override this behavior you can replace the self.dynamicType with the class it self
class A{func test(){Swift.print(String(self.dynamicType))}}
class B:A{}
class C{func test(){Swift.print(String(C))}}
A().test()//Output:A
B().test()//Output:B
C().test()//Output:C
```

### Updated example: 

```swift
/**
 * Create instances dynamically
 */
protocol X{var s:String{get};init(_ s:String)}
class A:X{var s:String;required init(_ s:String){self.s = s}}
class B:X{var s:String;required init(_ s:String){self.s = s}}
var instance:X
var classType:X.Type

classType = A.self
instance = classType.init("abc")
print(instance.s)//abc

classType = B.self
instance = classType.init("123")
print(instance.s)//123
```