## **Casting Array<Any> to Array<String>**<!--more--> 

```swift
let arrayOfAny:Array<Any> = [1,"2",3.0,CGFloat(4)]
let stringArray:Array<String> = arrayOfAny.map {String($0)}
print(stringArray)//"1", "2", "3.0","4.0"
```

**Conclusion:**  
Sometimes its useful to convert from one array type to another. But be aware that the .map method iterates over the entire array and returns a "new copy" <--(sitation needed for this statement). The best approach is in most cases not to convert the array type but to either do instance checking before packing the array or instance checking after unpacking the array

**NOTE:**  
you can also do this (only applicable with AnyObject arrays):

```swift
let someArray:Array<AnyObject> = ["1","a","xyz"]
let stringArray:Array<String> = someArray as! Array<String>
print(stringArray)//["1","a","xyz"]
```

**NOTE:**  
it also seems you can down cast an array if the array extends the class at some point (This only works in one direction aka: upcasting not downcasting)

**NOTE:**  
if you use flatMap instead of map you remove all nil values in that array

```swift
let a:[String?] = ["a",nil,"b",nil]
let b:[String] = a.flatMap{$0}//you can also do map{$0!}<--flat map flattens nested arrays, in that case use map
Swift.print(b)//["a", "b"]
```

**NOTE:**
It seems using .map is the only way to downcast an array. And it seems to not create copies but real references

```swift
let list:Array<NSXMLElement> = xml.children as! Array<NSXMLElement>//xml.children returns an array with NSXMLNode items But NSXMLNode extends NSXMLElement, so it will work
```


Map example: 

```swift
class A:B{
    var value:String = "default"
    init(_ value:String){
        self.value = value
    }
}
class B{
    var someNum:CGFloat = 1
    init(){
        
    }
}
var arr1:Array<A> = [A("a"),A("b"),A("c")]

let arr2:Array<B> = arr1.map { $0 as B }//arr1 as! [A]//
arr2

//Further testing should be done to verify if the original refrences are intact
```