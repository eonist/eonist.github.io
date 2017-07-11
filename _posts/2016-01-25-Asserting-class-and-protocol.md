Great method that asserts if an instance is of a class type or of a protocol type <!--more--> 


```swift
	protocol IDescribable:class{var text:String{get}}
	class A:IDescribable{var text:String;init(_ text:String){self.text = text}}
	class B:A{}
	class C{}


	let a = A("I am a")
	let b = B("I am b")
	let c = C()

	func ofType<T>(instance:Any?,_ type:T.Type) -> T?{/*<--we use the ? char so that it can also return a nil*/
	    if(instance as? T != nil){return instance as? T}
	    return nil
	}

	Swift.print(ofType(a,A.self)!.text)//I am a
	Swift.print(ofType(a,IDescribable.self)!.text)//I am a
	Swift.print(ofType(b,B.self)!.text)//I am b
	Swift.print(ofType(c,C.self))//instance of c
```

**For future reference there is also these available:** 

```swift
class func isOfClassType(obj:AnyObject,_ classType:AnyClass)->Bool{
    return obj.isMemberOfClass(classType)
}
/**
 * Alternative to the above
 */
class func isOfClassType2(obj:AnyObject, _ classType:AnyClass?, _ protocolType:AnyObject? = nil/**/)->Bool? {
    return object_getClass(obj) == classType!
}
```

**This also works** (not applicable inside a method where you pass the class type)

```swift
class A{}
class B:A{}
class C{}

let x = B()

x is A//true
x is B//true
x is C//false
```

**An early idea before I figured out i could use generics to solve the problem:**

```swift
String(instance.dynamicType) == NSStringFromProtocol(IPositional.self)
//As long as you can pull the name from the protocol you can use that to assert. dynamiccType should return the protocol of an instance
```




This is a modified version of my type assertion technique. I added the "as? AnyClass" assert so that the type can only be of protocol type. You didn't specify a specific protocol so I assume this is what you wanted. There might be a more elegant way of doing this but going through my notes and research about class assertion this is what I came up with.



```swift

import Foundation

protocol IDescribable:class{}
class A:IDescribable{}
class B:A{}

let a = A()
let b = B()

func ofType<T>(instance:Any?,_ type:T.Type) -> T?{/*<--we use the ? char so that it can also return a nil*/
    if(instance as? T != nil && type as? AnyClass == nil){return instance as? T}
    return nil
}

Swift.print(ofType(a,A.self))//nil
Swift.print(ofType(a,IDescribable.self))//A
Swift.print(ofType(b,B.self))//nil
```