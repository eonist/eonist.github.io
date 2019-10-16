My notes on NSOperation<!--more-->Operations can render assistance in concurrency

The NSOperationQueue adds a number of benefits. For example, you can specify the maximum number of queued operations that can run simultaneously. This makes it easy to control how many operations run at the same time or to create a serial operation queue

```swift
let operation = NSBlockOperation(block: { () -> Void in
    // Do Something
    ...
})

operationQueue.addOperation(operation)
```


```swift
let queue = OperationQueue()
queue.maxConcurrentOperationCount = 2

let operation1 = BlockOperation(block: {
  ...
})
operation1.qualityOfService = .userInitiated

let operation2 = BlockOperation(block: {
  ...
})

operation1.completionBlock = {
    ...
}
operation2.completionBlock = {
    ...
}

operation2.addDependency(operation1)

queue.addOperation(operation1)
queue.addOperation(operation2)

```

## Subclassing NSOperation (adding more functionality)

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
