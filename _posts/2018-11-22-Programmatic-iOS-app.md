My checklist when making a programmatic iOS app<!--more-->

1. Clean up the `AppDelegate.swift` class and add a Window variable:
2. Delete `Main storyboard file base name` in the info.plist (since xCode 11 also remove scenedelegate)
3. Now you can delete ViewController.swift `and` Main.storyboard (since xCode 11 also remove scenedelegate)
4. Add MainVC and MainView
5. `CMD + R` Run the app, if the background is orange it works

```swift
import UIKit
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
   lazy var window: UIWindow? = createWindow()
   func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
      _ = window
      return true
   }
}
extension AppDelegate {
   func createWindow() -> UIWindow {
      let win = UIWindow(frame: UIScreen.main.bounds)
      let vc = ViewController()
      win.rootViewController = vc
      win.makeKeyAndVisible() // Important since we have no Main storyboard anymore
      return win
   }
}
class ViewController: UIViewController {
   override func viewDidLoad() {
      super.viewDidLoad()
      view = View()
      view.backgroundColor = .orange
   }
   override var prefersStatusBarHidden: Bool { return true }
}
class View: UIView {
   override init(frame: CGRect) {
      super.init(frame: frame)
   }
   /**
    * Boilerplate
    */
   required init?(coder aDecoder: NSCoder) {
      fatalError("init(coder:) has not been implemented")
   }
}
```

### References:
- https://ioscoachfrank.com/remove-main-storyboard.html
