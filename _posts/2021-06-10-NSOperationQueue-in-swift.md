My notes on queuing things with NSOperationQueue in swift<!--more-->

> NSOperation is an object that can be subclassed, added to NSOperationQueues, etc. DispatchWorkItem is more "lightweight"

### Use NsOperationQueue when:
NSOperation can be scheduled with a set of dependencies at a particular queue priority and quality of service. Unlike a block scheduled on a GCD queue, an NSOperation can be cancelled and have its operational state queried. And by subclassing, NSOperation can associate the result of its work on itself for future reference.
- Network requests
- Image resizing
- Text processing
- or any other repeatable structured, long-running task that produces associated state or data.

### When not to use NsOperationQueue:
- For one-off computation, or simply speeding up an existing method, it will often be more convenient to use a lightweight GCD dispatch than employ NSOperation.

### Main benefits of NSOperationQueue (pros):
- Dependencies, preventing operations start before the previous ones are finished. Dependencies also work between different operation queues and threads.
- Support of the additional completion block.
- Monitoring operations changes of state by using KVO.
- Support of operations priorities and influencing their execution order.
- Cancellation option, allowing to stop the operation at the time of it's execution.
- Has ability to run nested queues ✨
- Has ability to run concurrent queues
- Has ability to run serialized queues (set max to 1) (execute one after the other after the previous finish)

### Cons:
- Does not support retry?
- ASync operations require subclassing + a bit complicated code

### Overarching benefits of Operations:
- They are reusable within your project, and often between projects.
- They are multi-core aware, so you’re always using as many cores as possible on the device.
- They know about thread quality of service, so the OS can make intelligent decisions about resource management.
- They are priority-aware, meaning you can tell the system what’s important (loading more tweets) vs. what’s not-so-important (downloading user avatars).
- They are testable, making your code robust ᕦ(ò_óˇ)ᕤ

### Notes:
- What is NsOperationQueue in Swift? A queue that regulates the execution of operations.
- NSOperation represents a single unit of work. It’s an abstract class that offers a useful, thread-safe structure for modeling state, priority, dependencies, and management.
- Examples of tasks that lend themselves well to NSOperation include network requests, image resizing, text processing, or any other repeatable, structured, long-running task that produces associated state or data.
- GCD is ideal for in-line asynchronous processing, NSOperation provides a more comprehensive, object-oriented model of computation for encapsulating all of the data around structured, repeatable tasks in an application.
- An NSOperation can be **cancelled** and have it's operational state queried.
- Once you dispatch a task using Grand Central Dispatch, you no longer have control or insight into the execution of that task. **This is what NSOperation solves!**

```swift
let backgroundOperation = NSOperation()
backgroundOperation.queuePriority = .Low
backgroundOperation.qualityOfService = .Background

let operationQueue = NSOperationQueue.mainQueue()
operationQueue.addOperation(backgroundOperation)
```

```swift
let networkingOperation: NSOperation = ...
let resizingOperation: NSOperation = ...
resizingOperation.addDependency(networkingOperation)

let operationQueue = NSOperationQueue.mainQueue()
operationQueue.addOperations([networkingOperation, resizingOperation], waitUntilFinished: false)
```

**Completion block:**

```swift
let operation = NSOperation()
operation.completionBlock = {
    print("Completed")
}

NSOperationQueue.mainQueue().addOperation(operation)
```

### Research
- https://www.raywenderlich.com/1197-nstask-tutorial-for-os-x#toc-anchor-001
- Linked lists: https://www.raywenderlich.com/947-swift-algorithm-club-swift-linked-list-data-structure (related)

### Subclasses:
- For situations where it doesn’t make sense to build out a custom NSOperation subclass,
- Foundation provides the concrete implementations [NSBlockOperation](https://developer.apple.com/documentation/foundation/blockoperation) and [NSInvocationOperation](https://developer.apple.com/documentation/foundation/nsinvocationoperation)

### NSOperationQueue:
- If the operations in a queue have a similar priority, they are executed by the FIFO principle. (first in, first out)
- Example in how to make NSOperationQueue async with subclassing: https://stackoverflow.com/a/24943851/5389500 (swift version lower on the page)
- Wait until the prior one finishes: With an NSOperationQueue, you achieve that simply by setting **maxConcurrentOperationCount** to 1.
- Serialize operations on a given context. That happens naturally if you use a single thread, but NSOperationQueue also serializes its operations if you set **maxConcurrentOperationCount** to 1
- Link: https://developer.apple.com/documentation/foundation/nsoperationqueue
- If the operation does not fully respond to the application’s needs, an NSOperation subclass can be created to add the missing functionality.
- Use Operation because you’re dealing with a table view and, for performance and power consumption reasons, **you need the ability to cancel an operation for a specific image** if the user has scrolled that image off the screen. Even if the operations are on a background thread, if there are dozens of them waiting on the queue, **performance will still suffer.**

### Calls:
- `addOperation`: Adds the specified operation to the receiver
- `addOperations:waitUntilFinished:` Adds the specified operations to the queue.
- `addOperationWithBlock:` Wraps the specified block in an operation and adds it to the receiver.
- `addBarrierBlock:` Invokes a block when the queue finishes all enqueued operations, and prevents subsequent operations from starting until the block has completed
- `addBarrierBlock`: Invokes a block when the queue finishes all enqueued operations, and prevents subsequent operations from starting until the block has completed.)
- `cancelAllOperations` - Cancels all queued and executing operations
- `waitUntilAllOperationsAreFinished` - Blocks the current thread until all the receiver’s queued and executing operations finish executing
- `maxConcurrentOperationCount` The maximum number of queued operations that can run at the same time
- `progress` An object that represents the total progress of the operations executing in the queue

### Force a NSOperationQueue runs on one thread:
There are 2 ways you could potentially handle this:

1. Make the operation queue serial. Set the maxConcurrentOperationCount property of the queue to 1, so that even if you add the operations to the queue at the same time, they'll only be executed in the order in which they were added.
2. Make operation1 dependent on operation0. If you need to keep the operation queue concurrent, you can use NSOperation.addDependency(_:) to explicitly make sure that operation1 will only begin once operation0 has completed.

In operation1, make sure you call refresh() on the Realm object you're using to fetch your Realm object in order to make absolutely sure that the changes you made in operation0 have been properly exposed on that queue.

### Lifecycle of an Operation:
These states are controlled by 4 KVO-enabled boolean properties: isReady, isExecuting, isFinished and isCancelled.

- Operations start in the Pending state when they are first added to a queue. Nothing really interesting to see here. The 4 flags are all false
- At some point, when an Operation becomes ready to execute, it moves to the Ready state. It’s now eligible to be executed by the OperationQueue. isReady now equals true and all the other flags equal false.

(Actually, there’s a little more that goes into Operations becoming Ready, and we’ll talk more about it in part 2 of this series.)

- Then the operation moves to the Executing state. This means it’s currently doing its thing (i.e. executing its main() method). isReady and isExecuting equal true and the other 2 flags equal false.
- Finally, when it’s done, the operation moves to the Finished state. At this point, isExecuting equals false and isFinished equals true. Do notice, however, that this state provides no indication as to whether or not the operation was successful. This’ll be important to keep in mind for later ☝️
- Also, at any point before it’s finished, an Operation can be cancelled. This moves it to the Cancelled state (isCancelled is true).

### Cancelling Operations
Cancelling means exactly what you would expect it to: abort whatever you are doing and clean up as soon as possible. You can cancel an Operation by using its cancel() method and you can check if it was cancelled by using its cancelled property.

There’s a catch to this though. Since the meaning of “cancel” can change from operation to operation, it’s up to you to periodically check the isCancelled flag during the execution of your operation in order to stop execution and/or clean up any ongoing tasks. This could amount to cancelling a URLSessionTask, for example.

### Suspend queues:
The isSuspended flag allows you to start and stop an operation queue. In the next article, we’ll see how we can make use of this property for some interesting behavior. By default, queues are not suspended (i.e. isSuspended is false).

### maxConcurrentOperationCount
Allows you to set how many operations you want to execute at once. By default, it’ll run as many as possible. Usually this is fine. By setting this value to 1, you can create a serial queue.


### Example (swift)
```swift
lazy var imgSaveQueue: OperationQueue = {
    var queue = OperationQueue()
    queue.name = "Image Save Queue"
    queue.maxConcurrentOperationCount = 1
    return queue
}()
// Add operation to it
imgSaveQueue.addOperation(BlockOperation(block: {
 	//your image saving code here
}))
```

### Logging operation example:

```swift
An Operation that logs to the console looks something like this:

class LoggingOperation : Operation {
   // When the Operation becomes ready to execute, the OperationQueue will run its main() method. Once the main() method is completed, the operation’s isFinished flag gets set to true and the operation gets removed from the queue.
    override func main() {
        if isCancelled { return }
        print("Logging operation")
    }
}
```

### References
- Great read on NSOperation [https://nshipster.com/nsoperation/](https://nshipster.com/nsoperation/)
- Explains addDependency: [https://stackoverflow.com/questions/39100653/how-adddependency-method-works-in-nsoperationqueue/39100827](https://stackoverflow.com/questions/39100653/how-adddependency-method-works-in-nsoperationqueue/39100827)
- Passing data from one operation to the other: [https://ioscoachfrank.com/chaining-nsoperations.html](https://ioscoachfrank.com/chaining-nsoperations.html)
- Creating umbrella operations with many queues: (abstract away common sequences of tasks etc) [https://ioscoachfrank.com/grouping-operations.html](https://ioscoachfrank.com/grouping-operations.html)
- Apple doc: [https://developer.apple.com/documentation/foundation/operationqueue](https://developer.apple.com/documentation/foundation/operationqueue)  and [https://developer.apple.com/documentation/dispatch/dispatchqueue](https://developer.apple.com/documentation/dispatch/dispatchqueue)
- Operations: [https://developer.apple.com/documentation/foundation/operation](https://developer.apple.com/documentation/foundation/operation)

### Questions:
- What about timeout for tasks?

### Colloquy:
- **Task:** a simple, single piece of work that needs to be done.
- **Thread:** a mechanism provided by the operating system that allows multiple sets of instructions to operate at the same time within a single application.
- **Process:** an executable chunk of code, which can be made up of multiple threads.

### Snippet to support async closures waiting:
Alternatively to the code bellow, you could eschew the use of NSOperation and use a dispatch_group_t to get similar behavior; call dispatch_group_enter() initially, call dispatch_group_leave() in the download task's completion handler, and use dispatch_notify() to run your completion block.

```swift
open class AsyncOperation: Operation {
    override open var isAsynchronous: Bool { true }
    override open var isExecuting: Bool { self.state == .started }
    override open var isFinished: Bool { self.state == .done }
    private enum State {
        case initial
        case started
        case done
    }
    private var state: State = .initial {
        willSet { // due to a legacy issue, these have to be strings. Don't make them key paths.
            self.willChangeValue(forKey: "isExecuting")
            self.willChangeValue(forKey: "isFinished")
        }
        didSet {
            self.didChangeValue(forKey: "isFinished")
            self.didChangeValue(forKey: "isExecuting")
        }
    }
    public init(name: String? = nil) {
        super.init()
        if #available(macOS 10.10, *) {
            self.name = name
        }
    }
    final override public func start() {
        self.state = .started
        self.main {
            if case .done = self.state {
                fatalError("AsyncOperation completion block called twice")
            }
            self.state = .done
        }
    }
    final override public func main() {}
    open func main(completionHandler: @escaping () -> ()) {
        fatalError("Subclass must override main(completionHandler:)")
    }
}
open class AsyncBlockOperation: AsyncOperation {
    private let closure: (_ completionHandler: @escaping () -> ()) -> ()
    public init(name: String? = nil, closure: @escaping (_ completionHandler: @escaping () -> ()) -> ()) {
        self.closure = closure
        super.init(name: name)
    }
    override open func main(completionHandler: @escaping () -> ()) {
        self.closure(completionHandler)
    }
}
```

### More modern variation:
From here: https://medium.com/shakuro/nsoperation-and-nsoperationqueue-to-improve-concurrency-in-ios-e31ee79c98ef

```swift
class AsynchronousOperation: Operation {
   enum State: String {
      case Ready
      case Executing
      case Finished
      private var keyPath: String { "is" + self.rawValue }
   }
   var state: State = .Ready {
      willSet {
         willChangeValue(forKey: newValue.rawValue)
         willChangeValue(forKey: newValue.rawValue)
      }
      didSet {
         didChangeValue(forKey: oldValue.rawValue)
         didChangeValue(forKey: oldValue.rawValue)
      }
   }
   override var isAsynchronous: Bool { true }
   override var isExecuting: Bool { state == .Executing }
   override var isFinished: Bool { state == .Finished }
   override func start() {
      if self.isCancelled {
         state = .Finished
      } else {
         state = .Ready
         main()
      }
   }
   override func main() {
      if self.isCancelled {
         state = .Finished
      } else {
         state = .Executing // Asynchronous logic (eg: n/w calls) with callback {
      }
   }
}
```

### And another variation:
We’ve effectively created a base class for all the concurrent operations we’ll create in the future. It overrides both isExecuting and isFinished, as well as adding a new method called completeOperation(). This is the method we’re going to call in order to tell the rest of the system that our operation is, in fact, finished.

But beware! When using this base class, we’ll need to make sure that all code paths lead to completeOperation(). Otherwise, the operation will never finish, and thus never get removed from the queue.

```swift
class ConcurrentOperation: Operation {
    private var backing_executing : Bool
    override var isExecuting : Bool {
        get { backing_executing }
        set {
            willChangeValue(forKey: "isExecuting")
            backing_executing = newValue
            didChangeValue(forKey: "isExecuting")
        }
    }
    private var backing_finished : Bool
    override var isFinished : Bool {
        get { backing_finished }
        set {
            willChangeValue(forKey: "isFinished")
            backing_finished = newValue
            didChangeValue(forKey: "isFinished")
        }
    }
    override init() {
        backing_executing = false
        backing_finished = false
        super.init()
    }
    func completeOperation() {
        isExecuting = false
        isFinished = true
    }
}
```

### Super class for the above
```swift
class FetchPokemonOperation : ConcurrentOperation {
    private var task: URLSessionDataTask?
    var data: AnyObject?
    override func main() {
        if isCancelled {
	    completeOperation()
            return
        }
        let session = URLSession(configuration: .ephemeral)
        let request = URLRequest(url: URL.init(string: "http://pokeapi.co/api/v2/pokemon/151/")!)
        task = session.dataTask(with: request, completionHandler: { (data, response, error) in
            if self.isCancelled {
	        self.completeOperation()
                return
            }
            // Be a good citizen and handle errors, ok? :)
            let parsedResponse = try! JSONSerialization.jsonObject(with: data!)
            self.data = parsedResponse as AnyObject
            print(self.data!)
	    self.completeOperation()
        })
        task?.resume()
    }
}
```

### Photo app example code:
This simple class represents each photo displayed in the app, together with its current state, which defaults to .new. The image defaults to a placeholder.

Ref: https://www.raywenderlich.com/5293-operation-and-operationqueue-tutorial-in-swift

```swift
// This enum contains all the possible states a photo record can be in
enum PhotoRecordState {
  case new, downloaded, filtered, failed
}
class PhotoRecord {
  let name: String
  let url: URL
  var state = PhotoRecordState.new
  var image = UIImage(named: "Placeholder")
  init(name:String, url:URL) {
    self.name = name
    self.url = url
  }
}
```

To track the status of each operation, you’ll need a separate class. Add the following definition to the end of PhotoOperations.swift:

```swift
class PendingOperations {
  lazy var downloadsInProgress: [IndexPath: Operation] = [:]
  lazy var downloadQueue: OperationQueue = {
    var queue = OperationQueue()
    queue.name = "Download queue"
    queue.maxConcurrentOperationCount = 1
    return queue
  }()
  lazy var filtrationsInProgress: [IndexPath: Operation] = [:]
  lazy var filtrationQueue: OperationQueue = {
    var queue = OperationQueue()
    queue.name = "Image Filtration queue"
    queue.maxConcurrentOperationCount = 1
    return queue
  }()
}
```
Note: The methods for downloading and filtering images are implemented separately, as there is a possibility that while an image is being downloaded the user could scroll away, and you won’t yet have applied the image filter. So next time the user comes to the same row, you don’t need to re-download the image; you only need to apply the image filter! Efficiency rocks! :]

Downloader
```swift
class ImageDownloader: Operation {
  let photoRecord: PhotoRecord //1
  init(_ photoRecord: PhotoRecord) { //2
    self.photoRecord = photoRecord
  }
  override func main() { //3
    if isCancelled { return }//4
    guard let imageData = try? Data(contentsOf: photoRecord.url) else { return } //5
    if isCancelled { //6
      return
    }
    if !imageData.isEmpty { //7
      photoRecord.image = UIImage(data:imageData)
      photoRecord.state = .downloaded
    } else {
      photoRecord.state = .failed
      photoRecord.image = UIImage(named: "Failed")
    }
  }
}
```
**filter**
```swift
class ImageFiltration: Operation {
  let photoRecord: PhotoRecord
  init(_ photoRecord: PhotoRecord) {
    self.photoRecord = photoRecord
  }
  override func main () {
    if isCancelled { return }
    guard self.photoRecord.state == .downloaded else { return }
    if let image = photoRecord.image,
       let filteredImage = applySepiaFilter(image) {
      photoRecord.image = filteredImage
      photoRecord.state = .filtered
    }
  }
  func applySepiaFilter(_ image: UIImage) -> UIImage? {
     guard let data = UIImagePNGRepresentation(image) else { return nil }
     let inputImage = CIImage(data: data)
     if isCancelled { return nil }
     let context = CIContext(options: nil)
     guard let filter = CIFilter(name: "CISepiaTone") else { return nil }
     filter.setValue(inputImage, forKey: kCIInputImageKey)
     filter.setValue(0.8, forKey: "inputIntensity")
     if isCancelled { return nil }
     guard
       let outputImage = filter.outputImage,
       let outImage = context.createCGImage(outputImage, from: outputImage.extent)
     else {
       return nil
     }
     return UIImage(cgImage: outImage)
   }
}

```

**In the table code:**

```swift
func startOperations(for photoRecord: PhotoRecord, at indexPath: IndexPath) {
  switch (photoRecord.state) {
  case .new:
    startDownload(for: photoRecord, at: indexPath)
  case .downloaded:
    startFiltration(for: photoRecord, at: indexPath)
  default:
    NSLog("do nothing")
  }
}

func startDownload(for photoRecord: PhotoRecord, at indexPath: IndexPath) {
  guard pendingOperations.downloadsInProgress[indexPath] == nil else { //1
    return
  }
  let downloader = ImageDownloader(photoRecord) //2
  downloader.completionBlock = { //3
    if downloader.isCancelled {
      return
    }
    DispatchQueue.main.async {
      self.pendingOperations.downloadsInProgress.removeValue(forKey: indexPath)
      self.tableView.reloadRows(at: [indexPath], with: .fade)
    }
  }
  pendingOperations.downloadsInProgress[indexPath] = downloader //4
  pendingOperations.downloadQueue.addOperation(downloader) //5
}

func startFiltration(for photoRecord: PhotoRecord, at indexPath: IndexPath) {
  guard pendingOperations.filtrationsInProgress[indexPath] == nil else {
      return
  }
  let filterer = ImageFiltration(photoRecord)
  filterer.completionBlock = {
    if filterer.isCancelled {
      return
    }
    DispatchQueue.main.async {
      self.pendingOperations.filtrationsInProgress.removeValue(forKey: indexPath)
      self.tableView.reloadRows(at: [indexPath], with: .fade)
    }
  }
  pendingOperations.filtrationsInProgress[indexPath] = filterer
  pendingOperations.filtrationQueue.addOperation(filterer)
}
if !tableView.isDragging && !tableView.isDecelerating {
  startOperations(for: photoDetails, at: indexPath)
}
override func scrollViewWillBeginDragging(_ scrollView: UIScrollView) {
  suspendAllOperations()//1
}
override func scrollViewDidEndDragging(_ scrollView: UIScrollView, willDecelerate decelerate: Bool) {
  if !decelerate { // 2
    loadImagesForOnscreenCells()
    resumeAllOperations()
  }
}
override func scrollViewDidEndDecelerating(_ scrollView: UIScrollView) {
  loadImagesForOnscreenCells() // 3
  resumeAllOperations()
}
// More and full project code at the bottom: https://www.raywenderlich.com/5293-operation-and-operationqueue-tutorial-in-swift
```

### Example (objc):
```objc
NSOperationQueue *queue = [[NSOperationQueue alloc] init];
queue.maxConcurrentOperationCount = 4;   // generally with network requests, you don't want to exceed 4 or 5 concurrent operations;
                                         // it doesn't matter too much here, since there are only 3 operations, but don't
                                         // try to run more than 4 or 5 network requests at the same time
NSOperation *operation1 = [[NetworkOperation alloc] initWithRequest:request1 completionHandler:^(NSData *data, NSError *error) {
    [self doSomethingWithData:data fromRequest:request1 error:error];
}];
NSOperation *operation2 = [[NetworkOperation alloc] initWithRequest:request2 completionHandler:^(NSData *data, NSError *error) {
    [self doSomethingWithData:data fromRequest:request2 error:error];
}];
NSOperation *operation3 = [[NetworkOperation alloc] initWithRequest:request3 completionHandler:^(NSData *data, NSError *error) {
    [self doSomethingWithData:data fromRequest:request3 error:error];
}];
[operation2 addDependency:operation1];   // don't start operation2 or 3 until operation1 is done
[operation3 addDependency:operation1];

[queue addOperation:operation1];         // now add all three to the queue
[queue addOperation:operation2];
[queue addOperation:operation3];
```

### Todo:
- Figure out how to add timeout to NSOperation
- Figure out how NSOperation is started, can it be added to while it runs etc? Answer: it begins when you add something to the queue i thin
