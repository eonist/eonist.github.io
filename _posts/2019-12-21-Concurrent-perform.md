My notes on Parallel Execution <!--more-->

### concurrentPerform
Grand Central Dispatch implements an efficient parallel for-loop. It must be called on a specific queue not to accidentally block the main one:

```swift
DispatchQueue.global().async {
    DispatchQueue.concurrentPerform(iterations: 999) { index in
        // Do something
    }
}
```

### Another example:

```swift
var total = 0

let syncQueue = DispatchQueue(label: "...")

DispatchQueue.concurrentPerform(iterations: maxY) { y in
    var subTotal = 0
    for x in 0..<maxX {
        if ... {
            subTotal += 1
        }
    }
    syncQueue.sync {
        total += subTotal
    }
}

print(total)
```

### Stride:
```swift
let lock = NSLock()

let stride = maxY / 20
let iterations = Int((Double(height) / Double(stride)).rounded(.up))

DispatchQueue.concurrentPerform(iterations: iterations) { i in
    var subTotal = 0
    let range = i * stride ..< min(maxY, (i + 1) * stride)
    for y in range {
        for x in 0 ..< maxX {
            if ... {
                subTotal += 1
            }
        }
    }

    lock.sync { count += subTotal }
}
```

## Gotchas:
- Sometimes doing concurrentPerform on the inner loop is more performant than on the outer loop
- release build is faster than debug builds
- You can ⭐simulate release build speed⭐ by: setting optimizations turned off: xcode project target -> swift compiler optimizations:  -> Debug -> optimizd for speed
- You can do any number of iterations with concurrentPerform:, only up to 8 threads will be scheduled.
- Always run concurrentPerform: in a Global queue.

## Resources:
- Alot of info on concurrency in swift: https://www.uraimo.com/2017/05/07/all-about-concurrency-in-swift-1-the-present/
