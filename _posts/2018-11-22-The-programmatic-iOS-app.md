My checklist when making a programmatic iOS app<!--more-->

1. Clean up the AppDelegate class:

```swift
import UIKit
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
   var window: UIWindow?
   func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
      _ = window
      return true
   }
}
```

2. Delete `Main storyboard file base name` in the info.plist
3. Now you can delete ViewController.swift
4. Add MainVC and MainView

```swift
class MainVC :UIViewController{
	override func viewDidLoad() {
      super.viewDidLoad()
      view = MainView()
      view.backgroundColor = .orange
   }
}
class MainView:UIView{}
```

5. Add a Window and a ViewController to the `AppDelegate.swift` and ref `_ = window`:

```swift
lazy var window: UIWindow? = {
   let win = UIWindow(frame: UIScreen.main.bounds)
   let vc = MainVC()
   win.rootViewController = vc
   win.makeKeyAndVisible()/*Important since we have no Main storyboard anymore*/
   return win
}()
```

6. `CMD + R` Run the app, if the background is orange it works
