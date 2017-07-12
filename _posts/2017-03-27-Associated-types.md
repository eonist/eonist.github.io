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

See the mosby example for a more advance example: [here](http://eon.codes/blog/2017/03/22/mosby-swift/)  

http://www.russbishop.net/swift-associated-types