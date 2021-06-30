<!--more-->


### thoughts on Using native navigation class
- Works for simple cases

### Thoughts on using custom nav class:
- Supports responsive table view setups
- Supports custom look and feel
- Supports custom translucent background that blends into the background color


### Gotchas:
- UIBarButtonItem is used for backbutton
- to go back use. `navigationController?.popViewController(animated: true)`
### Extra
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
