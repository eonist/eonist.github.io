My notes on performance in swift<!--more-->When dealing with swift objects in general, we sometimes forget about memory issues or safety, because Swift is handling it for us. But it comes with a cost or performance.

Once your done with the array, remember to dealocate it

Often, the optimizer can eliminate bounds checks within an array algorithm, but when that fails, invoking the same algorithm on the buffer pointer passed into your closure lets you trade safety for speed.

The following example shows how you can iterate over the contents of the buffer pointer:

```swift
// The pointer passed as an argument to body is valid only during the execution of withUnsafeBufferPointer(_:). Do not store or return the pointer for later use.
func unsafeArray() {
   let numbers = [1, 2, 3, 4, 5]
   let sum = numbers.withUnsafeBufferPointer { buffer -> Int in
    var result = 0
    for i in stride(from: buffer.startIndex, to: buffer.endIndex, by: 2) {
        result += buffer[i]
    }
    return result
   }
 }
```

### While loop
while loop is often faster than for loop on big sets:
```swift
let capacity: Int = 1000000000
var i: Int = 0
while i < capacity {
   functor(i) // dos stuff here
   i = i &+ 1
}
```

## ContiguousArray
ContiguousArray makes sure all items in the array contiguous with each other, and this is very helpful to find the next element. But, as usual, we are dealing with a tradeoff, and there is no magic here. Using ContiguousArray practically saying that we are adding constraints for the array management, and simple actions such as inserting or appending can be heavier now, because we are forcing all its elements to be contiguous, so it depends on your use case here.
- Ref: https://medium.com/@nitingeorge_39047/swift-array-vs-contiguousarray-a6153098a5
- Ref: https://medium.com/every-thing-swift/swift-array-vs-contiguousarray-9e156e8f3647
- might want to set reserveCapacity before adding to ContiguousArray
- Some say ContiguousArray has no effect ref: https://forums.swift.org/t/performance-advantage-of-contiguousarray/18142
- Basically, whenever you would store classes or @objc protocol types in an array, you might want to consider using ContiguousArray instead of an Array. ref: https://stackoverflow.com/questions/31290576/what-s-the-difference-between-arrayt-contiguousarrayt-and-arrayslicet-in


## Autorelease pools
The Apple instructions for creating autoreleasepools say that developers typically will not need to do it themselves. There are some cases, however, where creating your own can help reduce the peak memory footprint of your app. Autoreleasepools help because instead of waiting for the system to release whatever objects you have made, you are telling the system to release those objects at the end of the block (or closure in Swift). This example will help explain:

```swift
for (int i=0; i<5000; i++) {
   @autoreleasepool{
   NSNumber *num = [NSNumber numberWithInt:i];
   num performOperationOnNumber];
   }
}
```

**Autorelease side effects:**
The only "negative" impact (I know) by using the autoreleasepool too often is that you decrease the performance of your app because you ask more often the runtime to clean up its memory. Yes you can try adding a second autoreleasepool arround the if let secondImage =
