My notes on creating a custom tab bar in swift<!--more-->

### Resources:
- Custom TabBar and navigatorBar: [https://github.com/BartSimpsons/BartRootTaabbarViewController](https://github.com/BartSimpsons/BartRootTaabbarViewController) and tutorial: [https://programmer.help/blogs/swift-special-custom-uitabbar-uitabbarcontroller-and-uinavigation-controller.html](https://programmer.help/blogs/swift-special-custom-uitabbar-uitabbarcontroller-and-uinavigation-controller.html) 
- View embedder code: [https://stackoverflow.com/a/47764799/5389500](https://stackoverflow.com/a/47764799/5389500) 
- Embedding view controllers within view controllers: ✨ [https://stackoverflow.com/a/44333386/5389500](https://stackoverflow.com/a/44333386/5389500) 
- Simple Embedding vc inside vc:  [https://stackoverflow.com/q/56837437/5389500](https://stackoverflow.com/q/56837437/5389500) 
- Apples docs on custom View controller setups: [https://developer.apple.com/documentation/uikit/view_controllers/creating_a_custom_container_view_controller](https://developer.apple.com/documentation/uikit/view_controllers/creating_a_custom_container_view_controller)
- Managing state with ViewController hierarchies: [https://www.swiftbysundell.com/articles/custom-container-view-controllers-in-swift/](https://www.swiftbysundell.com/articles/custom-container-view-controllers-in-swift/)
- Great break down of UIViewController hierarchy: [https://cocoacasts.com/managing-view-controllers-with-container-view-controllers/](https://cocoacasts.com/managing-view-controllers-with-container-view-controllers/)

### Gotchas:
- Establishing a container-child relationship between view controllers prevents UIKit from interfering with your interface unintentionally. UIKit normally routes information to each of your app’s view controllers independently. When a container-child relationship exists, UIKit routes many requests through the container view controller first, giving it a chance to alter the behavior for any child view controllers. For example, a container view controller may override the traits of its children, forcing them to adopt a specific appearance or behavior.
- By using a `container view controller`, a user interface can be split up into logical or functional components, each managed by a view controller.
- Remember that a container view controller is responsible for sizing and positioning the view of the child view controller it manages.
- Why use ViewController instead of just view? A view controller gets access to events like viewDidLoad and viewWillAppear, even when used as a child, which can be really useful for many kinds of UI code.
- **Containment API**: The basic idea is that a UIViewController is not limited to containing views. It can contain other UIViewControllers as well. It is then the container’s responsibility to manage the children and decide what to show, when, and how to handle their interactions.
- UIView do have life cycle didMoveToSuperview that can be used to avoid excessive VC usage?

### Add a child UIViewController
```swift
/**
 * Add view and controller
 * - Fixme: ⚠️️ consider moving the sizing code into a closure that can support .frame or autolayout?
 * - Note: Key difference of using VC is that the childVC receives events about appearing and disssapearing
 */
private func add(asChildViewController viewController: UIViewController) {
	 addChild(viewController) // // Add Child View Controller (attaches events that notify child VC about appearing and dissapearing)
    view.addSubview(viewController.view) // Add Child View as Subview (Add the child’s root view to your container’s view hierarchy.)
    viewController.view.frame = view.bounds // Configure Child View
    viewController.view.autoresizingMask = [.flexibleWidth, .flexibleHeight]
    viewController.didMove(toParentViewController: self) // Notify Child View Controller (all the didMove(toParent:) method of the child view controller to notify it that the transition is complete.)
}
```
### Removing a child UIViewController:
```swift
/**
 * Remove view and controller
 */
private func remove(asChildViewController viewController: UIViewController) {
    guard parent != nil else { return } // Just to be safe, we check that this view controller, is actually added to a parent before removing it.
    viewController.willMove(toParentViewController: nil) // Notify Child View Controller
    viewController.view.removeFromSuperview() // Remove Child View From Superview
    viewController.removeFromParentViewController() // Notify Child View Controller
}
```

### Toggle UIViewController's
```swift
enum State { case session, summary }
private func toggle(state: State) {
    if state == .summary {
        remove(asChildViewController: sessionsViewController)
        add(asChildViewController: summaryViewController)
    } else { // session
        remove(asChildViewController: summaryViewController)
        add(asChildViewController: sessionsViewController)
    }
}
```

### Detecting appearing and disappearing
```swift
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    print("Summary View Controller Will Appear")
}
override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)
    print("Summary View Controller Will Disappear")
}
```

### Setting size or adding constraints:
```swift
NSLayoutConstraint.activate([ // Add constraints to set the size and position of the child’s root view.
	 controller.view.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 10),
	 controller.view.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -10),
	 controller.view.topAnchor.constraint(equalTo: view.topAnchor, constant: 10),
	 controller.view.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -10)
])
// or we can use the frame:
viewController.view.frame = view.bounds // Add constraints to set the size and position of the child’s root view.
viewController.view.autoresizingMask = [.flexibleWidth, .flexibleHeight] // // enable auto-sizing (for example, if the device is rotated)
```

### Remove a Child View Controller from Your Content
To remove a child view controller from your container, perform the following steps in order:
- Call the child’s willMove(toParent:) method with the value nil.
- Deactivate or remove any constraints for the child’s root view.
- Call removeFromSuperview() on the child’s root view to remove it from the view hierarchy.
- Call the child’s removeFromParent() method to finalize the end of the container-child relationship.
- Breaking a container-child relationship tells UIKit that your container view controller is no longer displaying the child’s content. You can still maintain other references to the child view controller. For example, UINavigationController manages a stack of child view controllers, but it maintains a container-child relationship with only one or two of those children at any given time.
