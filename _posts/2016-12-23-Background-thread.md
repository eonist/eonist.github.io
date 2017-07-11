UI on the Main-thread everything else on a background thread<!--more--> 

## Terminology:

**Synchronously**
A synchronous function returns only after the completion of a task that it orders.

**Asynchronously**
An asynchronous function, on the other hand, returns immediately, ordering the task to be done but not waiting for it. Thus, an asynchronous function does not block the current thread of execution from proceeding on to the next function.

**Critical Section**  
This is a piece of code that must not be executed concurrently, that is, from two threads at once. This is usually because the code manipulates a shared resource such as a variable that can become corrupt if it’s accessed by concurrent processes.

**Serial Queues**  
- Since no two tasks in a serial queue can ever run concurrently, there is no risk they might access the same critical section at the same time
- executes only one task at a time and that it executes the tasks in the order they were added to the queue.

**Concurrent Queues**  
- guaranteed to start in the order they were added… and that’s about all you’re guaranteed! Items can finish in any order
- you have no knowledge of the time it will take for the next task to start, nor the number of tasks that are running at any given time

**Structs**
Swift’s built-in data types, like Array and Dictionary, are implemented as structs, and as a result there will seemingly be a lot of copying in your code when passing collections back and forth. Don’t worry about the memory usage implications of this. The Swift collection types are optimized to only make copies when necessary, for instance when an array passed by value is modified for the first time after being passed.


## Queue type examples:  
  
- **async - concurrent**: the code runs on a background thread. Control returns immediately to the main thread (and UI). The block can't assume that it's the only block running on that queue  

```swift
func doLongASyncTaskInConcurrentQueue() {
      let concurrentQueue = DispatchQueue(label: "com.queue.Concurrent", attributes: .concurrent)
      for i in 1...5 {
          concurrentQueue.async {
              let imgURL = URL(string: "https://upload.wikimedia.org/wikipedia/commons/0/07/Huge_ball_at_Vilnius_center.jpg")!
              let _ = try! Data(contentsOf: imgURL)
              print("\(i) completed downloading")
          }
          print("\(i) executing")
      }  
  }
```
Task will run in different thread(other than main thread) when you use GCD. Async means execute next line do not wait until the block executes which results non blocking main thread. Since its concurrent queue, all are executed in the order they are added to queue but with different background threads attached to the queue .Remember they are not supposed to finish the task as the order they are added to the queue..Order of task differs each time threads are created as necessarily automatically.Task are executed in parallel. With more than that(maxConcurrentOperationCount) is reached, some tasks will behave as a serial until a thread is free.  

- **async - serial**: the code runs on a background thread. Control returns immediately to the main thread. The block can assume that it's the only block running on that queue    

```swift
 func doLongAsyncTaskInSerialQueue() {
    let serialQueue = DispatchQueue(label: "com.queue.Serial")
    for i in 1...5 {
        serialQueue.async {
            let imgURL = URL(string: "https://upload.wikimedia.org/wikipedia/commons/0/07/Huge_ball_at_Vilnius_center.jpg")!
            let _ = try! Data(contentsOf: imgURL)
            print("\(i) completed downloading")
        }
    }
}
```
Task will run in different thread(other than main thread) when you use GCD. Async means execute next line do not wait until the block executes which results non blocking main thread. Since its serial queue, all are executed in the order they are added to serial queue.Tasks executed serially are always executed one at a time by the single thread associated with the Queue.    

- **sync - concurrent**: the code runs on a background thread but the main thread waits for it to finish, blocking any updates to the UI. The block can't assume that it's the only block running on that queue (I could have added another block using async a few seconds previously)  

```swift
func doLongSyncTaskInConcurrentQueue() {
     let concurrentQueue = DispatchQueue(label: "com.queue.Concurrent", attributes: .concurrent)
     for i in 1...5 {
         concurrentQueue.sync {
             let imgURL = URL(string: "https://upload.wikimedia.org/wikipedia/commons/0/07/Huge_ball_at_Vilnius_center.jpg")!
             let _ = try! Data(contentsOf: imgURL)
             print("\(i) completed downloading")
         }
         print("\(i) executed")
     }
 }
```
Task will run in different thread(other than main thread) when you use GCD. Sync runs a block on a given queue and waits for it to complete which results in blocking main thread. Since its concurrent queue, all are executed in the order they are added to queue but with different threads. But with synchronous operation it behaves as this is the serial queue.  

- **sync - serial**: the code runs on a background thread but the main thread waits for it to finish, blocking any updates to the UI. The block can assume that it's the only block running on that queue  

```swift
func doLongSyncTaskInSerialQueue() {
    let serialQueue = DispatchQueue(label: "com.queue.Serial")
    for i in 1...5 {
        serialQueue.sync {
            let imgURL = URL(string: "https://upload.wikimedia.org/wikipedia/commons/0/07/Huge_ball_at_Vilnius_center.jpg")!
            let _ = try! Data(contentsOf: imgURL)
            print("\(i) completed downloading")
        }
    }
}
Task will run in different thread(other than main thread) when you use GCD. Sync runs a block on a given queue and waits for it to complete which results in blocking main thread. Since its serial queue, all are executed in the order they are added(FIFO).
```
  
**NOTE:** You can also combine: "async - concurrent" & "sync - serial":  

```swift
func doMultipleSyncTaskWithinAsynchronousOperation() {
    let concurrentQueue = DispatchQueue(label: "com.queue.Concurrent", attributes: .concurrent)
    concurrentQueue.async {
        let concurrentQueue = DispatchQueue.global(qos: DispatchQoS.QoSClass.default)
        for _ in 1...5 {
            concurrentQueue.sync {
                //do something one after the other
            }
            //do something all at once
        }
    }
}
```

## Queue priority: 
- **DISPATCH_QUEUE_PRIORITY_HIGH**   
Items dispatched to the queue will run at high priority, i.e. the queue will be scheduled for execution before any default priority or low priority queue.

- **DISPATCH_QUEUE_PRIORITY_DEFAULT**   
Items dispatched to the queue will run at the default priority, i.e. the queue will be scheduled for execution after all high priority queues have been scheduled, but before any low priority queues have been scheduled.

_ **DISPATCH_QUEUE_PRIORITY_LOW**   
Items dispatched to the queue will run at low priority, i.e. the queue will be scheduled for execution after all default priority and high priority queues have been scheduled.

- **DISPATCH_QUEUE_PRIORITY_BACKGROUND**   
Items dispatched to the queue will run at background priority, i.e. the queue will be scheduled for execution after all higher priority queues have been scheduled and the system will run items on this queue on a thread with background status as per setpriority(2) (i.e. disk I/O is throttled and the thread's scheduling priority is set to lowest value).
  
## Background thread example:  

```swift
Swift 1.2 through 2.3
let qualityOfServiceClass = QOS_CLASS_BACKGROUND
let backgroundQueue = dispatch_get_global_queue(qualityOfServiceClass, 0)
dispatch_async(backgroundQueue, {
    print("This is run on the background queue")
    dispatch_async(dispatch_get_main_queue(), { () -> Void in
        print("This is run on the main queue, after the previous code in outer block")
    })
})

```

## Question?
I still don't understand the difference between async concurrent and async serial. What is the implication of using either. They both run in the background not disturbing the UI. And why would you ever use sync? Isn't all code sync. one after the other? 

## Cancel tasks: 

```swift
if isRunning {
  buildTask.terminate()
}
```

This seems to have info about canceling operations on threads [here](http://stackoverflow.com/questions/27259570/how-can-an-nsoperationqueue-wait-for-two-async-operations?rq=1) 


## Debugging:

```swift
sleep(4)//great for testing asyncronisity 
```

## Resources: 

**Nice thread snippet for swift 2.0:**  
http://stackoverflow.com/a/30841417

**Async thread lib:**  
http://stackoverflow.com/a/35209235

**Background and main thread examples:**  
http://stackoverflow.com/questions/24056205/how-to-use-background-thread-in-swift

**Queue examples:**  
http://stackoverflow.com/questions/37805885/how-to-create-dispatch-queue-in-swift-3?noredirect=1&lq=1

**Better queue examples:**  
http://stackoverflow.com/questions/19179358/concurrent-vs-serial-queues-in-gcd/35810608#35810608

**Executing NSTask on a background thread:**  
[https://www.raywenderlich.com/125071/nstask-tutorial-os-x](https://www.raywenderlich.com/125071/nstask-tutorial-os-x) (This tutorial is a bit difficult to understand, but it may provide some insights) (there are a few bugs in this code that will cause some blocks to never be executed)


## TODO:

- **Check AMShell**  
http://www.harmless.de/cocoa-code.php

- **Check ATask Bolt, Promises** (these are thin wrappers / sugar))

