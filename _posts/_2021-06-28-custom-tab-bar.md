My notes on creating a custom tab bar in swift<!--more-->

### Resources:
- Custom Bar and navigator: https://github.com/BartSimpsons/BartRootTaabbarViewController and tutorial: https://programmer.help/blogs/swift-special-custom-uitabbar-uitabbarcontroller-and-uinavigation-controller.html
- View embedder code: https://stackoverflow.com/a/47764799/5389500
- Embedding view controllers within view controllers: ✨ https://stackoverflow.com/a/44333386/5389500
- Simple Embedding vc inside vc: https://stackoverflow.com/q/56837437/5389500
- Apples docs on custom View controller setups: https://developer.apple.com/documentation/uikit/view_controllers/creating_a_custom_container_view_controller
- Managing state with ViewController hierarchies: https://www.swiftbysundell.com/articles/custom-container-view-controllers-in-swift/


### Gotchas:
- Establishing a container-child relationship between view controllers prevents UIKit from interfering with your interface unintentionally. UIKit normally routes information to each of your app’s view controllers independently. When a container-child relationship exists, UIKit routes many requests through the container view controller first, giving it a chance to alter the behavior for any child view controllers. For example, a container view controller may override the traits of its children, forcing them to adopt a specific appearance or behavior.
- By using a container view controller, a user interface can be split up into logical or functional components, each managed by a view controller.

### Add a child UIViewController
```swift
private func add(asChildViewController viewController: UIViewController) {
    addChildViewController(viewController) // Add Child View Controller
    view.addSubview(viewController.view) // Add Child View as Subview
    viewController.view.frame = view.bounds // Configure Child View
    viewController.view.autoresizingMask = [.flexibleWidth, .flexibleHeight]
    viewController.didMove(toParentViewController: self) // Notify Child View Controller
}
```
### Removing a child UIViewController:
```swift
private func remove(asChildViewController viewController: UIViewController) {
    viewController.willMove(toParentViewController: nil) // Notify Child View Controller
    viewController.view.removeFromSuperview() // Remove Child View From Superview
    viewController.removeFromParentViewController() // Notify Child View Controller
}
```
### Embedding view controller
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    let controller = storyboard!.instantiateViewController(withIdentifier: "Second")
    addChild(controller)
    controller.view.translatesAutoresizingMaskIntoConstraints = false
    view.addSubview(controller.view) // Add the child’s root view to your container’s view hierarchy.
    NSLayoutConstraint.activate([ // Add constraints to set the size and position of the child’s root view.
        controller.view.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 10),
        controller.view.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -10),
        controller.view.topAnchor.constraint(equalTo: view.topAnchor, constant: 10),
        controller.view.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -10)
    ])
    controller.didMove(toParent: self)
}
```
### Another Example
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    setUpViews()
    let secondViewController = SecondViewController()
    secondViewController.willMove(toParent: self)
    containerView.addSubview(secondViewController.view) // Add the child’s root view to your container’s view hierarchy.
    // set the frame
    secondViewController.view.frame = containerView.bounds // Add constraints to set the size and position of the child’s root view.
    // enable auto-sizing (for example, if the device is rotated)
    secondViewController.view.autoresizingMask = [.flexibleWidth, .flexibleHeight]
    self.addChild(secondViewController)
    secondViewController.didMove(toParent: self) // all the didMove(toParent:) method of the child view controller to notify it that the transition is complete.
}
```

### Remove a Child View Controller from Your Content
To remove a child view controller from your container, perform the following steps in order:
- Call the child’s willMove(toParent:) method with the value nil.
- Deactivate or remove any constraints for the child’s root view.
- Call removeFromSuperview() on the child’s root view to remove it from the view hierarchy.
- Call the child’s removeFromParent() method to finalize the end of the container-child relationship.
- Breaking a container-child relationship tells UIKit that your container view controller is no longer displaying the child’s content. You can still maintain other references to the child view controller. For example, UINavigationController manages a stack of child view controllers, but it maintains a container-child relationship with only one or two of those children at any given time.
