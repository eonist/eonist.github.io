Notes on associatedtype <!--more--> 


```swift
protocol Thing {
    typealias argType
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

doThing(IntThing(), 2)//3
```

See your mosby example. 

http://www.russbishop.net/swift-associated-types