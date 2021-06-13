My notes on DispatchWorkItem<!--more-->

### Main benefits:
- Ability to cancel many tasks in the queue
- Simpler than NsOperationQueue
- Can be built for Search trotting functionality for instance

## Example:
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

### Resources:
- Canceling, resuming all items: [https://blog.natanrolnik.me/dispatch-work-item](https://blog.natanrolnik.me/dispatch-work-item)
- debouncer: https://github.com/onmyway133/blog/issues/376 and https://twitter.com/_inside/status/984827954432798723/photo/1
- Examples and description: [https://www.raywenderlich.com/5371-grand-central-dispatch-tutorial-for-swift-4-part-2-2#toc-anchor-005](https://www.raywenderlich.com/5371-grand-central-dispatch-tutorial-for-swift-4-part-2-2#toc-anchor-005)  
