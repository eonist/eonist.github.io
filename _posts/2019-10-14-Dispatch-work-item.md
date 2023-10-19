My notes on DispatchWorkItem<!--more-->

> A dispatch work item has a cancel flag. If it is cancelled before running, the dispatch queue won’t execute it and will skip it. If it is cancelled during its execution, the cancel property return True. In that case, we can abort the execution

### Main benefits:
- Ability to cancel many tasks in the queue
- Simpler than `NSOperationQueue`
- Can be built for Search trotting functionality for instance
- Has ability to wait until work is done (similar to semaphore)

## Basic example:
```swift
let workItem = DispatchWorkItem {
    sleep(4) // Your async code goes in here (sleep simulates work that takes 4 secs)
}
DispatchQueue.main.asyncAfter(deadline: .now() + 1, execute: workItem) // Execute the work item after 1 second
workItem.cancel()// You can cancel the work item if you no longer need it
```

## Another basic example:
```swift
let dwi = DispatchWorkItem {
    for i in 1...5 {
        print("DispatchWorkItem \(i)")
    }
}
// Perform on the current thread
dwi.perform()
// Perform on the global queue
DispatchQueue.global().async(execute: dwi)
```

## Example with cancel callback:
```swift
let queue = DispatchQueue(label: "com.swiftpal.dispatch.workItem")
let workItem = DispatchWorkItem() { //  Create a work item
    print("Stored Task")
}
queue.async(execute: workItem) // Task 1
queue.asyncAfter(deadline: DispatchTime.now() + 1, execute: workItem) // Task 2
workItem.cancel() // Work Item Cancel
queue.async(execute: workItem) // Task 3
if item.isCancelled { // KVO CALLBACK
    print("Task was cancelled")
}
/* Output:
 Stored Task
 Task was cancelled
 */

```

### Example with dependencies (creates serial dependency)
- This can be used to perform two or more tasks in a serial manner, where the first task has to be finished before executing the next one.
- An example use case would be fetching data for a table view on a background thread and notifying the main thread that the data is ready to use
- The perform() method on DispatchWorkItem will start the execution of the work item synchronously on the current thread.

```swift
class Controller {
	func getSomethingFromServer() {
		let newWorkItem = DispatchWorkItem { // work item to fetch something from server
			print ("II fetching data from the server"
			sleep(2) // async task to fetch results based on the query
		}
		let notifyTheView = DispatchWorkItem { // work item to notify the view that the info is ready
			print( "the info from server has arrived")
		}
		newWorkItem.notify(queue: .main) { // this get executed when newWorkItem has finished execution
			notifyTheView. perform( )
		}
		DispatchQueue. global(). async(execute:newWorkItem)
	}
}
let cont = Controller()
cont.getSomethingFromServer()
```

### Example: (another serial dependency example)
```swift
let dwi3 = DispatchWorkItem {
    print("start DispatchWorkItem")
    sleep(2)
    print("end DispatchWorkItem")
}
//this block will be executed on a the siqpatch queue 'dq' when dwi3 completes
let myDq = DispatchQueue(label: "A custom dispatch queue")
dwi3.notify(queue: myDq) {
    print("notify")
}
DispatchQueue.global().async(execute: dwi3)
```

### Example: (wait causes a task to be synchronous)
`wait()` causes the caller to wait synchronously until the dispatch work item finishes executing. Let’s see it in action.

```swift
class Controller {
   func getSomethingFromServer() {
      let newWorkItem = DispatchWorkItem { // work item to fetch something from server
         print ("fetching data from the server")
         print (Thread.current)
      } // async task to fetch results based on the query
      DispatchQueue.global().async(execute: newWorkItem) // starts the execution of new work item asynchronously
      newWorkItem.wait( ) // blocks the thread until newWorkitem finishes execution
      print ("finished execution") // This gets printed after newWorkitem finishes execution
   }
}
let controller = Controller()
controller.getSomethingFromServer( )
```

- It is not recommended to use to wait() as it blocks the thread from execution.
- In our example above, we are blocking the main thread from execution and that should be avoided. Even if we dispatch the task to one of the background threads, we are essentially blocking one of the very limited GCD worker threads while we perform the request.

### Combining DispatchWorkItem and DispatchGroup:
```swift
var storedError: NSError?
let downloadGroup = DispatchGroup()
var addresses = [PhotoURLString.overlyAttachedGirlfriend,
                 PhotoURLString.successKid,
                 PhotoURLString.lotsOfFaces]
addresses += addresses + addresses // You expand the addresses array to hold three copies of each image.
var blocks: [DispatchWorkItem] = [] // You initialize a blocks array to hold dispatch block objects for later use.
for index in 0..<addresses.count {
  downloadGroup.enter()
  let block = DispatchWorkItem(flags: .inheritQoS) { // You create a new DispatchWorkItem. You pass in a flags parameter to specify that the block should inherit its Quality of Service class from the queue you dispatch it to. Then, you define the work to do in a closure.
    let address = addresses[index]
    let url = URL(string: address)
    let photo = DownloadPhoto(url: url!) { _, error in
      if error != nil {
        storedError = error
      }
      downloadGroup.leave()
    }
    PhotoManager.shared.addPhoto(photo)
  }
  blocks.append(block)
  DispatchQueue.main.async(execute: block) // You dispatch the block asynchronously to the main queue. For this example, using the main queue makes it easier to cancel select blocks since it's a serial queue. The code that sets up the dispatch blocks is already executing on the main queue so you are guaranteed that the download blocks will execute at some later time.
}
for block in blocks[3..<blocks.count] { // You skip the first three download blocks by slicing the blocks array.
  let cancel = Bool.random() // Here you use Bool.random() to randomly pick between true and false. It's like a coin toss.
  if cancel {
    block.cancel() // If the random value is true, you cancel the block. This can only cancel blocks that are still in a queue and haven't began executing. You can't cancel a block in the middle of execution.
    downloadGroup.leave() // Here you remember to remove the canceled block from the dispatch group.
  }
}
downloadGroup.notify(queue: DispatchQueue.main) {
  completion?(storedError)
}
```

### Example: (another DispatchGroup example)
> The dispatch group allow to track the completion of different work items, even if they run on different queues.  

```swift
let dispatchWorkItem = DispatchWorkItem{
    print("work item start")
    sleep(1)
    print("work item end")
}

let dg = DispatchGroup()
//submit work items to the group
let dispatchQueue = DispatchQueue(label: "custom dq")
dispatchQueue.async(group: dg) {
    print("block start")
    sleep(2)
    print("block end")
}
DispatchQueue.global().async(group: dg, execute: dispatchWorkItem)
//print message when all blocks in the group finish
dg.notify(queue: DispatchQueue.global()) {
    print("dispatch group over")
}
```

### Example (use isCanceled flag to interrupt processing)

```swift
// create the dispatch work item
var dwi2:DispatchWorkItem?
dwi2 = DispatchWorkItem {
    for i in 1...5 {
        print("\(dwi2?.isCancelled)")
        if (dwi2?.isCancelled)!{
            break
        }
        sleep(1)
        print("DispatchWorkItem 2: \(i)")
    }
}
// submit the work item to the default global queue
DispatchQueue.global().async(execute: dwi2!)

// cancelling the task after 3 seconds
DispatchQueue.global().async{
    sleep(3)
    dwi2?.cancel()
}
```

### Example (search debouncer)
```swift
// ⚠️️ Code syntax eeds more cleanup
class Controller {
   var workltem: DispatchWorkItem?
   // This function gets triggered everytime user adds a new letter to query
   func getSearchResu1ts(query: String) {
      workltem? . cancel( )
      let newWorkItem = DispatchWorkItem {
         print ("sent a backend request for (query) II")
         //async task to fetch results based on the query
      }
      workltem = newWorkItem
      DispatchQueue . global( ) . asyncAfter( deadline: milliseconds (30), execute: newWorkItem)
   }
   let cont = Controller()
   cont . getSearchResu1ts (query, "s")
   cont . getSearchResu1ts (query : "sh"
   Thread . sleep( forTimelnterval : Double. random (in: 1..3))
   cont . getSearchResu1ts (query : "shi")
   cont . getSearchResu1ts (query : "shir")
}
```
In the getSearchResults method, the WorkItem will be executed only after the user hasn’t typed anything for 30 milliseconds. So, the output of the above execution will be:
`// sent a backend request for sh`
`// sent a backend request for shir`

We are essentially canceling the previous workItem when a new letter is typed within the specified delay. Since we set the main thread to sleep for more than 30 milliseconds after typing sh, the background thread started executing the WorkItem.
This can also be implemented for text fields that require server-side validation. Let’s say the user is entering a user name to register on our website, we can use the DispatchWorkItem as in the above example to validate if the user name is already taken

### DispatchWorkItemFlags
This flag defines a set of behaviours for a work item, such as its quality-of-service class and whether to create a barrier or spawn a new detached thread. The most commonly used flags are assignCurrentContext and barrier.
- barrier: Causes the work item to act as a barrier block when submitted to a concurrent queue. In a concurrent queue, multiple tasks are executed simultaneously on different threads.
When the work item with the barrier flag starts executing, all the tasks in the queue are temporarily suspended and will be resumed once this work item is finished.
- assignCurrentContext: Sets the attributes of the work item to match the attributes of the current execution context.

### Resources:
- Canceling, resuming all items: [https://blog.natanrolnik.me/dispatch-work-item](https://blog.natanrolnik.me/dispatch-work-item)
- debouncer: https://github.com/onmyway133/blog/issues/376 and https://twitter.com/_inside/status/984827954432798723/photo/1
- Examples and description: [https://www.raywenderlich.com/5371-grand-central-dispatch-tutorial-for-swift-4-part-2-2#toc-anchor-005](https://www.raywenderlich.com/5371-grand-central-dispatch-tutorial-for-swift-4-part-2-2#toc-anchor-005)  

## Example (cancel callback, but contrived 🤷, for reference):
```swift
var workItem: DispatchWorkItem?
workItem = DispatchWorkItem {
    for i in 1..<6 {
        guard let item = workItem, !item.isCancelled else { // interrupt processing
            print("cancelled"); break
        }
        sleep(1)
        print(String(i))
    }
}
workItem?.notify(queue: .main) {
    print("done")
}
DispatchQueue.global().asyncAfter(deadline: .now() + .seconds(2)) {
    workItem?.cancel() // stop after 2 items are processed etc
}
DispatchQueue.main.async(execute: workItem!)
// you can use perform to run on the current queue instead of queue.async(execute:)
// Output 0, 1, 2, cancelled, done
```

## Example: (for reference)
```swift
class SearchViewController: UIViewController, UISearchBarDelegate {
    private var pendingRequestWorkItem: DispatchWorkItem? // We keep track of the pending work item as a property
    func searchBar(_ searchBar: UISearchBar, textDidChange searchText: String) {
        pendingRequestWorkItem?.cancel() // Cancel the currently pending item
        let requestWorkItem = DispatchWorkItem { [weak self] in // Wrap our request in a work item
            self?.resultsLoader.loadResults(forQuery: searchText)
        }
        pendingRequestWorkItem = requestWorkItem // Save the new work item and execute it after 250 ms
        DispatchQueue.main.asyncAfter(deadline: .now() + .milliseconds(250), execute: requestWorkItem)
    }
}
```

## Throttler
```swift
protocol Throttable {
    func perform(with delay: TimeInterval,
                 in queue: DispatchQueue,
                 block completion: @escaping () -> Void) -> () -> Void
}

extension Throttable {
    func perform(with delay: TimeInterval,
                 in queue: DispatchQueue = DispatchQueue.main,
                 block completion: @escaping () -> Void) -> () -> Void {

        var workItem: DispatchWorkItem?

        return {
            workItem?.cancel()
            workItem = DispatchWorkItem(block: completion)
            queue.asyncAfter(deadline: .now() + delay, execute: workItem!)
        }
    }
}
```
