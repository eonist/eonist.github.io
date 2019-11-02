My notes on DispatchWorkItem<!--more-->


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

// 1
addresses += addresses + addresses

// 2
var blocks: [DispatchWorkItem] = []

for index in 0..<addresses.count {
  downloadGroup.enter()

  // 3
  let block = DispatchWorkItem(flags: .inheritQoS) {
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

  // 4
  DispatchQueue.main.async(execute: block)
}

// 5
for block in blocks[3..<blocks.count] {

  // 6
  let cancel = Bool.random()
  if cancel {

    // 7
    block.cancel()

    // 8
    downloadGroup.leave()
  }
}

downloadGroup.notify(queue: DispatchQueue.main) {
  completion?(storedError)
}
```

### Resources:
- Canceling, resuming all items: [https://blog.natanrolnik.me/dispatch-work-item](https://blog.natanrolnik.me/dispatch-work-item) 
