My notes on customizing the native UINavigationController<!--more-->

### Thoughts on native nav controller:
- You can't see the code your subclassing
- Native nav controller is needed to get free vc transitions

### Wants:
- Implement a view controller that can hold some other view controllers.
- Show a tab bar at the bottom of the screen over the shown view controller.
- Switch between the various view controllers when the user taps on a tab bar button.
- support paginating new view controllers

### Gotchas:
- A navigation controller is a container view that can manage the navigation of hierarchical contents.
- Navigation stack can have “n” numbers of view controllers.
- The bottom-most view controller in that stack is the root view controller and others are child view controller.
- ⚠️️ You have to use native navigationController to get swipe left / right etc. ⚠️️ Or else build that logic in a custom project. Which is not easy. IF you want custom navbar, just hide the native one etc
- ⚠️️ You can strip down native uinavigationcontroller and just use its transitioning and vc array code. ⚠️️  THen roll your own navbar etc

### Resources:
- Apple docs: [https://developer.apple.com/documentation/uikit/uinavigationcontroller](https://developer.apple.com/documentation/uikit/uinavigationcontroller)
- really nice swipe transition possibly without darkened bug etc: [https://ordinarycoding.com/articles/simple-custom-uinavigationcontroller-transitions/](https://ordinarycoding.com/articles/simple-custom-uinavigationcontroller-transitions/)

### NavController scope:
```swift
// “FirstVC”
// “SecondVC”
```

### Utils

```swift
var navVCArray = self.navigationController?.viewControllers
navVCArray?.removeLast() //Remove the last view controller
navVCArray?.removeFirst() // Remove the first view controller
navVCArray?.removeAll() //Remove all view controllers
navVCArray?.remove(at: 0) //Remove at specific position
```

### Customization of native NavBar:
```swift
// Change “barTintColor”, “tintColor”
self.navigationController?.navigationBar.isTranslucent = false
self.navigationController?.navigationBar.barTintColor = .cyan
self.navigationController?.navigationBar.tintColor = .brown
// Set setBackgroundImage, shadowImage
navigationController?.navigationBar.setBackgroundImage(UIImage(named: "Banner2"), for: .default)
navigationController?.navigationBar.shadowImage = UIImage(named: "Banner")
// Set Title and customize title color
self.navigationItem.title = "First VC"
let textAttributes = [NSAttributedString.Key.foregroundColor:UIColor.red]
navigationController?.navigationBar.titleTextAttributes = textAttributes
// Set an image as navbar title
let logo = UIImage(named: "edit")
let imageView = UIImageView(image:logo)
self.navigationItem.titleView = imageView
// Clear your navbar background
self.navigationBar.setBackgroundImage(UIImage(), for: UIBarMetrics.default)
self.navigationBar.shadowImage = UIImage()
self.navigationBar.isTranslucent = true
self.view.backgroundColor = UIColor.clear
// Remove your navbar border
self.navigationBar.setBackgroundImage(UIImage(), for:.default)
self.navigationBar.shadowImage = UIImage()
self.navigationBar.layoutIfNeeded()
// Customize the back button. Remove the title of the back button and set the color.
navigationItem.backBarButtonItem = UIBarButtonItem(title: "", style: .plain, target: nil, action: nil)
navigationItem.backBarButtonItem?.tintColor = UIColor.cgLightBlue
// Add a bar button item: We can add leftBarButtonItem & rightBarButtonItem using system icon, text or custom image. Add a leftBarButtonItem with barButtonSystemItem add
navigationItem.leftBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(addTapped))
// Add rightBarButtonItem with “Add” title
navigationItem.rightBarButtonItem = UIBarButtonItem(title: "Add", style: .plain, target: self, action: #selector(addTapped))
// Create a bar button with own customization
let playButton = UIButton(type: .custom)
playButton.setImage(UIImage(named: "plus"), for: .normal)
playButton.addTarget(self, action: #selector(playTapped), for: .touchUpInside)
playButton.frame = CGRect(x: 0, y: 0, width: 10, height: 10)
let barButton = UIBarButtonItem(customView: playButton)
// Add multiple rightBarButtonItem
let add = UIBarButtonItem(barButtonSystemItem: .camera, target: self, action: #selector(addTapped))
let playButton = UIButton(type: .custom)
playButton.setImage(UIImage(named: "plus"), for: .normal)
playButton.addTarget(self, action: #selector(playTapped), for: .touchUpInside)
playButton.frame = CGRect(x: 0, y: 0, width: 10, height: 10)
let barButton = UIBarButtonItem(customView: playButton)
navigationItem.rightBarButtonItems = [add, barButton]
```
