My notes on semaphores<!--more-->

- Semaphores has the ability to convert-a-callback based method into a returning-style-method. ✨
- Semaphores also has the ability to timeout 👌
- As we already know, unlimited work might lead to a deadlock.
- Here is how we can apply dispatch semaphore to limit a queue to 3 concurrent tasks:
- Fixme: ⚠️️write about making callback method returnable in swift tips blog post?  
- Fixme: 👉 Make a wrapper for making methods that are async sync 👈 this seems harder than it sounds

### Making async call a return method:

```swift
func makeAPICall() -> String {
    var result: String = ""
    let semaphore = DispatchSemaphore(value: 0)
    DispatchQueue.global(qos: .background).async { // We need to put this on the main thread or else transition becomes glitchy
      sleep(2)
      result = "happy days"
      semaphore.signal()
    }
    semaphore.wait() // (wallTimeout: .distantFuture) // timeout: DispatchTime.now() + 1
    return result
}
```

### Example (Timeout)
> We can also specify a timeout for the wait function.

```swift
let sem = DispatchSemaphore(value: 0)
DispatchQueue.global().async {
    print("waiting for at least one signal for 1 second")
    let res = sem.wait(timeout: DispatchTime.now() + 1) //wait for a signal
    if(res == .timedOut){
        print("wait timed out")
    }else{
        print("At least one signal has been received")
    }
}
DispatchQueue.global().async {
    sleep(3)
    print("calling signal")
    sem.signal() //send the signal
}
```

### Limiting concurrent tasks:
As we already know, unlimited work might lead to a deadlock. Here is how we can apply dispatch semaphore to limit a queue to 3 concurrent tasks:

```swift
let concurrentTasks = 3
let queue = DispatchQueue(label: "Concurrent queue", attributes: .concurrent)
let sema = DispatchSemaphore(value: concurrentTasks)
print("began")
for _ in 0..<999 {
    queue.async {
        // Do work
        sema.signal()
    }
    sema.wait()
}
print("ended") // Called when all 999 items where processed
```

### Testing network connection:
```swift
let url = URL(string: urlString)
// 1
let semaphore = DispatchSemaphore(value: 0)
let _ = DownloadPhoto(url: url!) { _, error in
  if let error = error {
    XCTFail("\(urlString) failed. \(error.localizedDescription)")
  }
  // 2
  semaphore.signal()
}
let timeout = DispatchTime.now() + .seconds(defaultTimeoutLengthInSeconds)
// 3
if semaphore.wait(timeout: timeout) == .timedOut {
  XCTFail("\(urlString) timed out")
}
```

### Array example:

```swift
extension Array where Element == DataSource {
    func load() throws -> NoteCollection {
        let semaphore = DispatchSemaphore(value: 0)
        var loadedCollection: NoteCollection?
        // We create a new queue to do our work on, since calling wait() on
        // the semaphore will cause it to block the current queue
        let loadingQueue = DispatchQueue.global()
        loadingQueue.async {
            // We extend 'load' to perform its work on a specific queue
            self.load(onQueue: loadingQueue) { collection in
                loadedCollection = collection
                // Once we're done, we signal the semaphore to unblock its queue
                semaphore.signal()
            }
        }
        // Wait with a timeout of 5 seconds
        semaphore.wait(timeout: .now() + 5)
        guard let collection = loadedCollection else {
            throw NoteLoadingError.timedOut
        }
        return collection
    }
}
let dataSources: [DataSource] = [
    localDataSource,
    iCloudDataSource,
    backendDataSource
]
do {
    let collection = try dataSources.load()
    output(collection)
} catch {
    output(error)
}
```

### Resources:
- https://www.fivestars.blog/articles/semaphores/
