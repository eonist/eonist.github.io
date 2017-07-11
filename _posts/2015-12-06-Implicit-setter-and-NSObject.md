<!--more--> 
When you subclass NSObject you cant use explicit and implicit setters at the same time because the compiler is reserving the setSomeValue method name.

```swift
protocol IPositional{
    var name:String{get set}
    func setName(name:String)
    func getName()->String
}
class A:NSObject,IPositional{
    var name:String = ""
    func setName(name:String){
        print(name)
        self.name = name
    }
    func getName()->String{
        return name
    }
}
let a:Any = A()
```
As soon as you remove the NSObject from being the superclass you can use the implicit setter again.  
**Solution:** Rename your explicit getter and setter variable: 

```swift
var nameValue:String = ""
func setName(name:String){
   nameValue = name
}
```