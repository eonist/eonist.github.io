My notes on distributing tasks to multiple cpu cores<!--more-->. The easiest way to perform a loop of calculations in parallel is concurrentPerform

> After writing this article, I made ParallelLoop 💞, a functional and easy to understand library for doing parallel tasks in swift.
[https://github.com/passbook/ParallelLoop](https://github.com/passbook/ParallelLoop)  

## Find num of available cores and threads
```swift
let processInfo = ProcessInfo()
print(processInfo.activeProcessorCount) // prints 8 for 4-core macbook (playground) probably  provides the number of logical cores, not the number of physical cores
```

## Find thread id:
```swift
print("Current thread \(Thread.current)")
```

Get additional thread info [https://stackoverflow.com/a/42645509/5389500](https://stackoverflow.com/a/42645509/5389500)

## Execute optimal num of tasks depending on cpu core count:

```swift
let numOfCores: Int = ProcessInfo().activeProcessorCount
DispatchQueue.concurrentPerform(iterations: numOfCores) { (i: Int) in
    let newValue: CGFloat = 4000 * 4000
}
```

## Background thread:

```swift
DispatchQueue.global(qos: .userInitiated).async {
    // concurrentPerform
}
```

## Populating an array with concurrentPerform:
```swift
pixels.withUnsafeMutableBufferPointer { pixelsPtr in
    DispatchQueue.concurrentPerform(iterations: threadCount) { thread in
        for number in thread*size ..< (thread+1)*size {
            let floating = Float(number)
            pixelsPtr[number] = SIMD3(floating, floating, floating)
        }
    }
}
```

## ConcurrentMap
- Simplifying parallelism by combining ConcurrentPerform and map like abilities
- A further study into concurrent map can be found here: [https://gist.github.com/dabrahams/ea5495b4cccc2970cd56e8cfc72ca761](https://gist.github.com/dabrahams/ea5495b4cccc2970cd56e8cfc72ca761)
- Old-school way of doing concurrentApply: [https://stackoverflow.com/a/26693953/5389500](https://stackoverflow.com/a/26693953/5389500)
- Similar approach using semaphore [https://stackoverflow.com/a/54774379/5389500](https://stackoverflow.com/a/54774379/5389500)
- Seems like a better approach unsafe pointers etc: [https://gist.github.com/alextrob/a4e9885f063e1d5ea02e77771c464b78](https://gist.github.com/alextrob/a4e9885f063e1d5ea02e77771c464b78)
```swift
extension Array {
	/**
    * - Note: This will block the thread you call it from (just like the non-concurrent map will), so make sure to dispatch this to a background queue.
	 * - Note: One needs to ensure that there is enough work on each thread to justify the inherent overhead of managing all of these threads. (E.g. a simple xor call per loop is not sufficient, and you'll find that it's actually slower than the non-concurrent rendition.) In these cases, make sure you stride (see Improving Loop Code that balances the amount of work per concurrent block). For example, rather than doing 5000 iterations of one extremely simple operation, do 10 iterations of 500 operations per loop. You may have to experiment with suitable striding values.
	 * - Note: on striding: https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/ThreadMigration/ThreadMigration.html#//apple_ref/doc/uid/TP40008091-CH105-SW2
	 * - Note: Barriers Let’s say you added a series of closures to a specific queue (with different durations) but you now want to execute a job only after all the previous asynchronous task are completed. You can use barriers to do it., Let’s add 20 tasks (that will sleep for a timeout of 1 second) to the concurrent queue we created previously and use a barrier to print something once the other jobs complete, we’ll do this specifying a flag DispatchWorkItemFlags.barrier in our final async call:
	 * - Note: The 20 tasks will be executed in parallel without a specific order by the concurrent queue and you’ll see those messages appearing in groups of a size equal to the number of execution cores of your Mac, but the final call will always be executed last.
    */
    public func concurrentMap<T>(_ transform: (Element) -> T) -> [T] {
        var results = [Int: T]()
        let queue = DispatchQueue(label: Bundle.main.bundleIdentifier! + ".sync", attributes: .concurrent)
        DispatchQueue.concurrentPerform(iterations: count) { index in
            let result = transform(self[index])
            queue.async { results[index] = result }
        }
        return queue.sync(flags: .barrier) {
            (0 ..< results.count).map { results[$0]! }
        }
    }
}
```

## Testing concurrentPerform serial vs parallel
```swift
class MyAppTests: XCTestCase {

    // calculate pi using Gregory-Leibniz series

    func calculatePi(iterations: Int) -> Double {
        var result = 0.0
        var sign = 1.0
        for i in 0 ..< iterations {
            result += sign / Double(i * 2 + 1)
            sign *= -1
        }
        return result * 4
    }
    func performHeavyTask(iteration: Int) {
        let pi = calculatePi(iterations: 100_000_000)

        print(iteration, .pi - pi)
    }
    func testSerial () {
        measure {
            for i in 0..<10 {
                self.performHeavyTask(iteration: i)
            }
        }
    }
    func testConcurrent() {
        measure {
            DispatchQueue.concurrentPerform(iterations: 10) { i in
                self.performHeavyTask(iteration: i)
            }
        }
    }
}
// MacBook Pro 2018 with 2.9 GHz Intel Core i9, with a release build the concurrent test took, on average, 0.247 seconds, whereas the serial test took roughly four times as long, 1.030 seconds.
```

// theadsafe wrapper
https://talk.objc.io/episodes/S01E90-concurrent-map
```swift
final class ThreadSafe<A> {
    // ...

    func atomically(_ transform: (inout A) -> ()) {
        queue.sync {
            transform(&self._value)
        }
    }
}
extension Array {
    func concurrentMap<B>(_ transform: @escaping (Element) -> B) -> [B] {
        let result = ThreadSafe(Array<B?>(repeating: nil, count: count))
        DispatchQueue.concurrentPerform(iterations: count) { idx in
            let element = self[idx]
            let transformed = transform(element)
            result.atomically {
                $0[idx] = transformed
            }
        }
        return result.value.map { $0! }

    }
}
```

## Gotchas
- concurrentPerform is a great way to enjoy concurrency in a for loop in such a way that you won't exhaust GCD worker threads.
- The tasks performed within the concurrentPerform should in them self be threadsafe, meaning they should be serial. Creating/reading a QR-Image is likley a paralell process

## Resources:
- Great resource for SyncroDictonary and SyncroArray: [https://stackoverflow.com/a/61257101/5389500](https://stackoverflow.com/a/61257101/5389500)
- Comprehensive on GDC: [https://medium.com/@aliakhtar_16369/concurrency-in-swift-grand-central-dispatch-part-1-945ff05e8863](https://medium.com/@aliakhtar_16369/concurrency-in-swift-grand-central-dispatch-part-1-945ff05e8863)
- Comprehensive speed chart for variouse concurrentMap implementations: https://gist.github.com/dabrahams/ea5495b4cccc2970cd56e8cfc72ca761
