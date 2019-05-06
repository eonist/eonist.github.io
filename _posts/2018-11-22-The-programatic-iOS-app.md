My checklist when making a programmatic iOS app<!--more-->

1. Clean up the `AppDelegate.swift` class and add a Window variable:

```swift
import UIKit
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
   lazy var window: UIWindow? = {
      let win = UIWindow(frame: UIScreen.main.bounds)
      let vc = MainVC()
      win.rootViewController = vc
      win.makeKeyAndVisible()/*Important since we have no Main storyboard anymore*/
      return win
   }()
   func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
      _ = window
      return true
   }
}
```

2. Delete `Main storyboard file base name` in the info.plist
3. Now you can delete ViewController.swift` and `Main.storyboard`
4. Add MainVC and MainView

```swift
import UIKit
class MainVC: UIViewController{
	override func viewDidLoad() {
      super.viewDidLoad()
      view = MainView()
      view.backgroundColor = .orange
   }
}
class MainView: UIView { }
```

5. `CMD + R` Run the app, if the background is orange it works
