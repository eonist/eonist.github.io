The great thing about this approach to copying Class instances is that it doesn't rely on NSObject or objc code, and most importantly it doesn't clutter up the "Data-Style-Class". <!--more--> Instead it extends the protocol that extends the "Data-Style-Class".  This way you can ==compartmentalize== better by having the copy code in another place than the data it self. The inheritance between classes is also taken care of as long as you model the protocols after the classes. Here is an example of this approach:

```swift
protocol IA{var text:String {get set}}
class A:IA{
    var text:String
    init(_ text:String){
        self.text = text
    }
}
extension IA{
    func copy() -> IA {
        return A(text)
    }
}
protocol IB:IA{var number:Int {get set}}
class B:A,IB{
    var number:Int
    init(_ text:String, _ number:Int){
        self.number = number
        super.init(text)
    }
}
extension IB{
    func copy() -> IB {
        return B(text,number)
    }
}
let original = B("hello",42)
var uniqueCopy = original.copy()
uniqueCopy.number = 15
Swift.print("uniqueCopy.number: " + "\(uniqueCopy.number)")//15
Swift.print("original.number: " + "\(original.number)")//42
```
