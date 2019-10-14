My note on debouncing method calls<!--more-->

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
