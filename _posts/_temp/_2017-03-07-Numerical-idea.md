An idea for Sugar for Number types <!--more--> 


```swift

protocol Numerical {
    init()
    static func + (lhs: Self, rhs: Self) -> Self
}
func +(left: Int, right: CGFloat) -> CGFloat {
    return CGFloat(left) + right
}
func +(left: CGFloat, right: Int) -> CGFloat {
    return left + CGFloat(right)
}

extension Int:Numerical {}
extension CGFloat:Numerical {}

let a:Int = 2
let b:CGFloat = 4.5
let c:CGFloat = a + b
Swift.print("c: " + "\(c)")
/**
 * a limitation is that you cant mix types. like int and cgfloat. only one type at the time
 */
extension Sequence where Iterator.Element:Numerical {
    var sum: Iterator.Element {
        return reduce(Iterator.Element(), +)
    }
}

let numbers:Array = [CGFloat(1), CGFloat(2.0), CGFloat(3), CGFloat(4.0)]
print(numbers.sum) // Prints: "10.0"
let numbers2:Array = [Int(1), Int(2.0), Int(3), Int(4.0)]
print(numbers2.sum)//10
```

### Sum different types:

```swift
let list:[Any] = ["2",CGFloat(3),Int(4)]
var sum:Int = 0
list.forEach{
    if($0 is Int){
        sum += $0 as! Int
    }else if($0 is CGFloat){
        sum += ($0 as! CGFloat).int
    }else{
        sum += ($0 as! String).int
    }
}
Swift.print("sum: " + "\(sum)")//9
```