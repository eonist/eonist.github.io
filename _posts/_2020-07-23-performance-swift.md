When dealing with swift objects in general, we sometimes forget about memory issues or safety, because Swift is handling it for us. But it comes with a cost or performance.

once your done with the array, remember to dealocate it
```swift
func unsafeArray() {
     let arr = ["a","c","b"]
     var x = 0
     arr.withUnsafeBufferPointer { unsafedArray -> () in
         for i in unsafedArray {
             x += i.count
         }
     }
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
