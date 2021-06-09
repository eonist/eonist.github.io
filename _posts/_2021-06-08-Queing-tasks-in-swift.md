My notes on queuing things in swift<!--more-->

### Gotchas:
- A queue is a list where you can only insert new items at the back and remove items from the front. This ensures that the first item you enqueue is also the first item you dequeue. First come, first serve!

-  A Queue fits quite well any use-cases where the information needs to be computed in the specific entry order. For instance, if you build a chat interface, you want to include each message in the same way they have been typed. Queueing them in the same order while sent to a backend service would be a nice way to do it.

### NSOperation
- What is Nsoperationqueue in Swift?
A queue that regulates the execution of operations.
- NSOperation represents a single unit of work. It’s an abstract class that offers a useful, thread-safe structure for modeling state, priority, dependencies, and management.
- Examples of tasks that lend themselves well to NSOperation include network requests, image resizing, text processing, or any other repeatable, structured, long-running task that produces associated state or data.
- GCD is ideal for in-line asynchronous processing, NSOperation provides a more comprehensive, object-oriented model of computation for encapsulating all of the data around structured, repeatable tasks in an application.
- An NSOperation can be **cancelled** and have its operational state queried.
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

Completion block:

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


### NSOperationQueue:
-  If the operations in a queue have a similar priority, they are executed by the FIFO principle. (first in, first out)
- Example in how to make NSOperationQueue async with subclassing: https://stackoverflow.com/a/24943851/5389500 (swift version lower on the page)
- wait until the prior one finishes: With an NSOperationQueue, you achieve that simply by setting **maxConcurrentOperationCount** to 1.
- serialize operations on a given context. That happens naturally if you use a single thread, but NSOperationQueue also serializes its operations if you set **maxConcurrentOperationCount** to 1
- Link: https://developer.apple.com/documentation/foundation/nsoperationqueue
- If the operation does not fully respond to the application’s needs, an NSOperation subclass can be created to add the missing functionality.

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

### Main benefits of NSOperationQueue
- Dependencies, preventing operations start before the previous ones are finished.
- Support of the additional completion block.
- Monitoring operations changes of state by using KVO.
- Support of operations priorities and influencing their execution order.
- Cancellation option, allowing to stop the operation at the time of its execution.

### Force a NSOperationQueue runs on one thread:
There are 2 ways you could potentially handle this:

1. Make the operation queue serial. Set the maxConcurrentOperationCount property of the queue to 1, so that even if you add the operations to the queue at the same time, they'll only be executed in the order in which they were added.

2. Make operation1 dependent on operation0. If you need to keep the operation queue concurrent, you can use NSOperation.addDependency(_:) to explicitly make sure that operation1 will only begin once operation0 has completed.

In operation1, make sure you call refresh() on the Realm object you're using to fetch your Realm object in order to make absolutely sure that the changes you made in operation0 have been properly exposed on that queue.


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
### Pseudo-code

```swift

```

### Queue:

```swift
struct Queue<T> {
  private var elements: [T] = []
  // Add
  mutating func enqueue(_ value: T) {
    elements.append(value)
  }
  // Remove
  mutating func dequeue() -> T? {
    guard !elements.isEmpty else { return nil }
    return elements.removeFirst()
  }
  // first
  var head: T? {
    return elements.first
  }
  // last
  var tail: T? {
     return elements.last
  }
  public var isEmpty: Bool {
     return list.isEmpty
  }
}
let queue = Queue<String>()
queue.enqueue("Adam")
queue.enqueue("Julia")
queue.enqueue("Ben")

// We have 3 customers to serve, we're going to serve them in order of arrived
let serving = queue.dequeue() // Adam
let nextToServe = queue.head // Julia
```

### References
- Great read on NSOperation https://nshipster.com/nsoperation/
- Explains addDependency: https://stackoverflow.com/questions/39100653/how-adddependency-method-works-in-nsoperationqueue/39100827


### Snippet to support async closures waiting:
Alternatively to the code bellow, you could eschew the use of NSOperation and use a dispatch_group_t to get similar behavior; call dispatch_group_enter() initially, call dispatch_group_leave() in the download task's completion handler, and use dispatch_notify() to run your completion block.

```swift
open class AsyncOperation: Operation {
    override open var isAsynchronous: Bool { return true }
    override open var isExecuting: Bool { return self.state == .started }
    override open var isFinished: Bool { return self.state == .done }
    private enum State {
        case initial
        case started
        case done
    }
    private var state: State = .initial {
        willSet {
            // due to a legacy issue, these have to be strings. Don't make them key paths.
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

```swift
class AsynchronousOperation: Operation {
   enum State: String {
      case Ready
      case Executing
      case Finished
      private var keyPath: String {
         get {
            return "is" + self.rawValue
         }
      }
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
   override var isAsynchronous: Bool {
      get {
         return true
      }
   }
   override var isExecuting: Bool {
      get {
         return state == .Executing
      }
   }
   override var isFinished: Bool {
      get {
         return state == .Finished
      }
   }
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
         state = .Executing
         //Asynchronous logic (eg: n/w calls) with callback {
      }
   }
}
```
