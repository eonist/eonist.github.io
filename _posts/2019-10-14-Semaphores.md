My notes on semaphores<!--more-->

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
