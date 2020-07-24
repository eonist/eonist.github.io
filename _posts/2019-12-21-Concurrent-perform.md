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

### Do many things simultaneously and call onComplete when things are done
```swift
/**
 * - Abstract: process data in parallel on a background thread and calls a onComplete when it's complete
 * ## Examples:
 * processData { Swift.print("✅") } // Output: start, 1, 2, 0, 3, ✅
 */
func processData(onComplete: @escaping () -> Void) {
   Swift.print("start")
   DispatchQueue.global().async {
      DispatchQueue.concurrentPerform(iterations: 4) { index in
         Swift.print("\(index)")
         sleep((1..<3).randomElement()!) // Wait for n secs
      }
      onComplete()
   }
}

```

## Using concurrentPerform with async network processes

```swift
func downloadSync(path: String) -> (Data?, URLResponse?, Error?) {
    var result: (Data?, URLResponse?, Error?)! = nil
    let semaphore = DispatchSemaphore(value: 0)
    let url = URL(string: path)!
    let request = URLRequest(url: url)
    URLSession.shared.dataTask(with: request) { data, response, error in // async operation
        result = (data, response, error)
        semaphore.signal()
    }.resume()
    semaphore.wait()
    return result
}

let paths = ["http://qiita.com/mono0926/items/c32c008384df40bf4e41",
             "http://qiita.com/mono0926/items/acef5cb3651620a355c3",
             "http://qiita.com/mono0926/items/139014be6c15e32b9696"]
DispatchQueue.concurrentPerform(iterations: paths.count) { i in
    let r = downloadSync(path: paths[i])
    print(String(data: r.0!, encoding: .utf8))
}

```

### Correct way to specify the QoS is to dispatch the call to concurrentPerform to the desired queue:
[https://developer.apple.com/videos/play/wwdc2017/706/?time=285](https://developer.apple.com/videos/play/wwdc2017/706/?time=285)
```swift
DispatchQueue.global(qos: .userInitiated).async {
    DispatchQueue.concurrentPerform(iterations: 3) { (i) in
        ...
    }
}
```

### Stride:
```swift
let lock = NSLock() // needed when accessing a variable from many threads

let stride = maxY / 20 // maybe set stride to num of cores?
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

    lock.sync { count += subTotal } // needed when accessing a variable from many threads
}
```

### Another example (untested):
- You should be able to update the total async in this example
```swift
var total = 0

let syncQueue = DispatchQueue(label: "...") // needed when accessing a variable from many threads

DispatchQueue.concurrentPerform(iterations: maxY) { y in
    var subTotal = 0
    for x in 0..<maxX {
        if ... {
            subTotal += 1
        }
    }
    syncQueue.sync { // needed when accessing a variable from many threads
        total += subTotal
    }
}

print(total)
```

### Avoid creating too many threads
It might be tempting to create a lot of queues to gain better performance in your app. Unfortunately, creating threads comes with a cost and you should, therefore, avoid excessive thread creation.
There are two common scenarios in which excessive thread creation occurs:
- Too many blocking tasks are added to concurrent queues forcing the system to create additional threads until the system runs out of threads for your app
- Too many private concurrent dispatch queues exist that all consume thread resources.

## Gotchas:
- Sometimes doing concurrentPerform on the inner loop is more performant than on the outer loop
- Release build is faster than debug builds
- You can ⭐simulate release build speed⭐ by: setting optimizations turned off: xcode project target -> swift compiler optimizations:  -> Debug -> optimizd for speed
- You can do any number of iterations with concurrentPerform:, only up to 8 threads will be scheduled.
- Always run concurrentPerform: in a Global queue.

## Resources:
- Alot of info on concurrency in swift: https://www.uraimo.com/2017/05/07/all-about-concurrency-in-swift-1-the-present/
- Lots of info nuggets on concurrent performance: https://gist.github.com/FWEugene/3861f0460c3e23f684e113f0f8d6947f
