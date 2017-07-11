<!--more--> 
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