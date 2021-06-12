My notes on different swift architectures<!--more-->

# MVC:

##### Model
**Models store your data, such as the names of products in a store**  
This is where the data resides. Things like persistence, model objects, parsers, managers, and networking code live there.

##### View
**Views render data for users, for example a table showing the list of products available**  
The View layer is the face of your app. It's classes are often reusable as they don’t contain any domain-specific logic. For example, a UILabel is a view that presents text on the screen, and it’s reusable and extensible.

##### Controller
**Controllers combine the two, by querying the model and converting its data to something views can show**  
The Controller mediates between the view and the model via the delegation pattern. In an ideal scenario, the controller entity won’t know the concrete view it’s dealing with. Instead, it will communicate with an abstraction via a protocol. A classic example is the way a UITableView communicates with its data source via the UITableViewDataSource protocol. Both data flows (Model,View) go through the Controller, which in turn decide what should happen with the data.

## MVC Example:
##### Controller
```swift
class Controller {
	let model: Model
	let view: View
	init() {
		self.model = .init(data: ["a", "b", "c"], onDataChange: onModelChange)
		self.view = .init()
		self.view.tableDelegate = self
		self.view.tableDataSource = model
	}
}
extension Controller {
	func onModelChange() {
		view.update()
	}
	func tableView(tableView: UITableView, didSelectRowAt: IndexPath) {
		print("Tapped: \(didSelectRowAt)")
	}
}
```

##### view
```swift
class View: UITableView {
	init(){
		// Register cells
		// Set table style
	}
}
extension View {
  func update() {
	  self.reloadTable()
  }
}
```

##### Model
```swift
class Model {
	var onDataChange: OnDataChange = { print("no callback attached") }
	let data: [String]
	init(data: [String]) {
		self.data = data
	}
}
extension Model: UITableViewDataSource {
  typealias OnDataChange = () -> Void
  func setData(data: [String]){
	  self.data = data
	  onDataChange()
  }
  func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
	  return data.count
  }
  func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
	  let cell: UITableViewCell = view.dequeueReusableCell(withIdentifier: "id", for: indexPath)
	  cell.text = "\(data[indexPath.row])"
  }
}
```

### Another approach could be to use a delegate instead of the callbacks
- Although callbacks has it's benefits: [https://medium.cobeisfresh.com/why-you-shouldn-t-use-delegates-in-swift-7ef808a7f16b](https://medium.cobeisfresh.com/why-you-shouldn-t-use-delegates-in-swift-7ef808a7f16b) a possible delegate protocol could be:

```swift
protocol ModelDelegate {
	func handleDataChange()
}
class Model {
	weak var delegate: ModelDelegate? // Setting the variable is weak is important, as the delegate may be deallocated before the model
}
extension Model {
	func setData(data: [String]) {
		self.data = data
	  delegate.handleDataChange()
	}
}
extension ViewController: ModelDelegate {
	func handleDataChange() {
		view.update()
	}
}
```

### Gotchas:
-  Models should ideally be simple data containers that don't contain much (if any) logic.
