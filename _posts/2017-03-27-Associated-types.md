Notes on associatedtype <!--more-->

```swift
protocol Thing {
    associatedtype argType
    func doit(val:argType) -> argType
}
class IntThing : Thing {
    func doit(val: Int) -> Int {
        return val + 1
    }
}
func doThing<A:Thing>(thing:A, val:A.argType) -> A.argType {
    return thing.doit(val)
}
doThing(IntThing(), 2) // 3
```

See the mosby example for a more advance example: [here](http://eon.codes/blog/2017/03/22/mosby-swift/)  

http://www.russbishop.net/swift-associated-types

### Associated type, powerful way of making protocols generic
```swift
protocol DataHolder {
    associatedtype T
    var items: [T] { get set }
    mutating func addItem(_ newItem: T)
}

extension DataHolder {
    mutating func addItem(_ newItem: T) {
        items.append(newItem)
    }
}

class NumberHolder : DataHolder {
    var items: [Int] = []
}

var numbers = NumberHolder()
numbers.addItem(0)
print(numbers.items)

class StringHolder : DataHolder {
    var items: [String] = []
}

var strings = StringHolder()
strings.addItem("One")
print(strings.items)
```
