My notes on customizing UINavigationBar<!--more-->

### Thoughts on Using native navigation class
- Works for simple cases
- You can't see the code your subclassing

### Thoughts on using custom nav class:
- Supports responsive table view setups
- Supports custom look and feel
- Supports custom translucent background that blends into the background color

### Gotchas:
- UIBarButtonItem is used for ´back-button´
- to go back use. `navigationController?.popViewController(animated: true)`

### Resources:
- Apple docs: https://developer.apple.com/documentation/uikit/uinavigationbar
- Customize native navbar anim: https://github.com/gontovnik/HidesNavigationBarWhenPushed
- Add alert-view bellow nav-bar: https://github.com/hemangshah/Zingle
- Customize color in transition: https://github.com/DanisFabric/RainbowNavigation and https://github.com/ltebean/LTNavigationBar

### Extra:
From here: https://stackoverflow.com/questions/44734628/swift-how-do-i-create-a-custom-uinavigationbar-and-add-a-custom-back-button

```swift
// Hide navbar
override func viewWillAppear(_ animated: Bool) {
    self.navigationController?.isNavigationBarHidden = true
}
// subclassing:
let navBar: UINavigationBar = {
    let view = UINavigationBar()
    view.backgroundColor = .clear
    view.isTranslucent = true
    view.translatesAutoresizingMaskIntoConstraints = false
    return view
}()
// You can set custom Back button as like below
self.navigationItem.hidesBackButton = true
let backButton = UIBarButtonItem(image: UIImage(named: "image_name"), style: .plain, target: self, action: #selector(Class.methodName))
backButton.tintColor = UIColor.white
self.navigationItem.leftBarButtonItem = backButton
```

### Custom API:

```swift
// leftBtn
// title
// rightBtn
```
