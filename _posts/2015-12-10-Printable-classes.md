
 
This is a great way to add context to "data container classes" as it is then easy to debug all the variables in the instance <!--more--> by Swift.print(instance) //prints whatever you assign in the description return clause  
(Some define this a Describable class)  

```swift
class A:CustomStringConvertible{
    var someNumber:Int
    var description: String { get{return String(someNumber)} }
    init(_ someNumber:Int){
        self.someNumber = someNumber
    }
}
print([A(2),A(6),A(-4)])//Output: 2,6,-4
```

**NOTE:** You may also add this description variable to an extension for a cleaner code structure. Just remember to extend the CustomStringConvertible from the extension.



### You can also make a method that describes many Printable instances: 

```swift
func printValues<T:CustomStringConvertible>(array:Array<T>){
    for item in array {
        print(item)
    }
}
```
