My note on debouncing method calls<!--more-->

### Simple debounce:

```swift
import UIKit
/**
 * Event
 */
extension SliderCell {
   /**
    * On slider change
    */
   @objc func sliderValueDidChange(sender: UISlider) {
      NSObject.cancelPreviousPerformRequests(withTarget: self)
      // Debounce: wait until the user stops typing to send search requests
      perform(#selector(update(with:)), with: sender, afterDelay: 0.01)
   }
}
extension SliderCell {
   /**
    *
    */
   @objc private func update(with sender: UISlider) {
      // Do stuff here
      let str: String = .init(format: "%.00f", sender.value)+".0"
      if str != valueLabel.text {
         valueLabel.text = str//"\(sender.value)"
         action(value: sender.value)
      }
   }
}

```


### Example
This example shows how you can debounce network calls on every keystroke while the user types in a search-field. In order to only call backend every 0.3seconds to avoid congestion.

```swift
import UIKit

extension TopBar: UITextFieldDelegate {
	 var tableView: UITableView { /*ref to tableView*/ }
    /**
     * When the user enters the search field
     */
    func textFieldShouldBeginEditing(_ textField: UITextField) -> Bool {
        return true
    }
	  /**
     * On text change began
     */
    func textFieldDidBeginEditing(_ textField: UITextField) {
        searchField.selectedTextRange = textField.textRange(from: textField.beginningOfDocument, to: textField.endOfDocument)
    }
    func textField(_ textField: UITextField, shouldChangeCharactersIn range: NSRange, replacementString string: String) -> Bool {
        return true
    }
	  /**
     * Dismisses the keyboard
     */
    func textFieldShouldReturn(_ textField: UITextField) -> Bool {
        searchField.resignFirstResponder()
        return true
    }
}
extension TopBar {
    /**
     * Perform search action on every Text change (0.3 delay, also cancels previous search action if you type fast etc)
     */
    @objc func textFieldDidChange(_ textField: UITextField) {
        NSObject.cancelPreviousPerformRequests(withTarget: self, selector: #selector(tableView.search), object: nil) // debounce previous call, if user types fast
        if searchField.text != "" {
            tableView.filterSearch()
            perform(#selector(tableView.search), with: nil, afterDelay: 0.3)
        } else { // no content
            User.getItems(user: User.currentUser, loc: (long:10.421906, lat:63.446827  )) { items in
                DispatchQueue.main.async { [weak self]
					 	  mainVC.tableView.sections = [TableView.SectionData.discoverSection(stories: stories)]
                    mainVC.tableView.reloadData() // reload the tableview
                }
            }
        }
    }

}
extension TableView {
	var searchField: UITextField { /*ref to searchfield*/ }
	/**
	 * Removes the current cells that doesn't match the search query (Aka filtering)
	 * - Fixme: ⚠️️ To make search even faster, cache the search results, and filter them
	 */
	func filterSearch(){
		 let newDiscoverSection: StoryTableView.SectionItem = {
			  let searchResults: [SearchResult] = self.rows.compactMap { row in
					if row.title != TableView.Category.explore && row.title.lowercased().contains(searchField.text) {
						 return .init(title: row.title, type: .approved, followers: 0, following: true, image: nil)
					} else {
						 return nil
					}
			  }
			  return StoryTableView.SectionData.discoverSection(searchResult: searchResults)
		 }()
		 DispatchQueue.main.async { [weak self]
			  self?.sections = [newDiscoverSection]
			  self?.reloadData() // reload the tableview
		 }
	}
	/**
	 * Search call
	 */
	@objc func search(){
		 Service.seachForItems(user: User.currentUser, seachString: searchField.text) { (searchResults: [SearchResult]) in
			  DispatchQueue.main.async { [weak self]
					let section: TableView.SectionItem = TableView.SectionData.discoverSection(searchResult: searchResults)
					self?.sections = [section]
					self?.reloadData() // reload the tableview
			  }
		 }
	}
}
```

### Resources:

Debouncing can also be done with DispatchWorkItem: [https://www.swiftbysundell.com/articles/a-deep-dive-into-grand-central-dispatch-in-swift/](https://www.swiftbysundell.com/articles/a-deep-dive-into-grand-central-dispatch-in-swift/)  and [https://blog.natanrolnik.me/dispatch-work-item](https://blog.natanrolnik.me/dispatch-work-item)


### Notification throttler:

```swift
class NotificationThrottler {
    let notificationCenter: NotificationCenter
    let timeInterval: TimeInterval
    let handler: () -> Void
    private var workItem: DispatchWorkItem?
    deinit {
        notificationCenter.removeObserver(self)
    }
    init(handler: @escaping () -> Void,
         notificationCenter: NotificationCenter = .default,
         notificationName: Notification.Name,
         timeInterval: TimeInterval) {
        self.handler = handler
        self.notificationCenter = notificationCenter
        self.timeInterval = timeInterval

        notificationCenter.addObserver(self,
                                       selector: #selector(notificationPosted),
                                       name: notificationName,
                                       object: nil)
    }
    @objc func notificationPosted() {
        workItem?.cancel()
        workItem = DispatchWorkItem(block: handler)

        //we just created the work item, it is safe to force unwrap in this situation
        DispatchQueue.main.asyncAfter(deadline: .now() + timeInterval, execute: workItem!)
    }
}
```


### A way to que up DispatchWorkItems:

```swift
import Foundation

class Dispatcher {
    private var items = [DispatcherIdentifier: DispatchWorkItem]()
    private let queue: DispatchQueue
    deinit {
        cancelAllActions()
    }
    init(_ queue: DispatchQueue = .main) {
        self.queue = queue
    }
    func schedule(after timeInterval: TimeInterval,
                  with identifier: DispatcherIdentifier,
                  on queue: DispatchQueue? = nil,
                  action: @escaping () -> Void) {
        cancelAction(with: identifier)
        print("Scheduled \(identifier)")
        let item = DispatchWorkItem(block: action)
        items[identifier] = item
        (queue ?? self.queue).asyncAfter(deadline: .now() + timeInterval, execute: item)
    }
    @discardableResult
    func cancelAction(with identifier: DispatcherIdentifier) -> Bool {
        guard let item = items[identifier] else {  return false }
        defer {   items[identifier] = nil }
        guard !item.isCancelled else {   return false }
        item.cancel()
        print("Cancelled \(identifier)")
        return true
    }
    func cancelAllActions() {
        items.keys.forEach {
            items[$0]?.cancel()
            items[$0] = nil
        }
    }
}
```
