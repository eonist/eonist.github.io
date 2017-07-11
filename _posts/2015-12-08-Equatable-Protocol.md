My notes on the Equatable protocol in swift <!--more--> 
```swift
protocol IA:Equatable{
    var someVariable:String{get set}
}
class A:IA{
    var someVariable:String
    init(_ someVariable:String){
        self.someVariable = someVariable
    }
}
func == <T : IA>(lhs: T, rhs: T) -> Bool {
    return lhs.someVariable == rhs.someVariable
}

let a1 = A("test")
let a2 = A("test")

a1 == a2//true
```

For classes:

```swift
class A:Equatable{
    var someVariable:String
    init(_ someVariable:String){
        self.someVariable = someVariable
    }
}
func == (lhs: A, rhs: A) -> Bool {
    return lhs.someVariable == rhs.someVariable
}

let a1 = A("test")
let a2 = A("test")

a1 == a2//true
```