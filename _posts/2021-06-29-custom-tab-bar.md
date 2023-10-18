My notes on customizing UITabBar<!--more-->

### Thoughts on extending UITabBar:
- You have to add `tabbaritem` to the viewController that it activates,
- You have to pull `tabbaritems` from the viewcontrollers in the tabbarviewcontroller
- There is a lot of apple cruft in the tabbar classes
- complicated to customize
- complicated to add support for SVG, Font icons
- you cant see the code your subclassing

### Thoughts on writing a TabBar from scratch:
- The code will be more isolated and easier to understand
- You can use viewcontroller embedding to embed the TabBar in a UINavigationController like setup
- Easier to get the look and feel you want (can support special translucent background)
- Usually when you get design from Figma, its all custom design that doesn't convert well to native design
- Native `TabBar` doesn't support selected state with custom design / background / animation
- More specific code to the use case and less boilerplate code to workaround apple shenanigans

### Gotchas:
- Pin the bottom of the tabbar to: `safeAreaInsets.bottom` to account for the notch (get this var from window if using autolayout, see utils method) [here](https://stackoverflow.com/questions/46829840/get-safe-area-inset-top-and-bottom-heights)

### Resources:
- Customizing UITabBar: [https://stackoverflow.com/a/57616340/5389500](https://stackoverflow.com/a/57616340/5389500)
- Making UITabBarController have drop-shadow and rounded corners: [https://github.com/mohammed-abuamra/custom-tabbarcontroller](https://github.com/mohammed-abuamra/custom-tabbarcontroller)
- Making UITabBar transparent: [https://dev-georgegarcia.medium.com/creating-a-fully-transparent-uitabbar-in-swift-ae689f3e4c51](https://dev-georgegarcia.medium.com/creating-a-fully-transparent-uitabbar-in-swift-ae689f3e4c51)
- Custom navcontroller + tabbar with uiviews as tabbaritem: [https://medium.com/sprinthub/creating-a-customized-tab-bar-in-ios-with-swift-41ed380f2a30 and github: https://github.com/quacklabs/customTabBarSwift](https://medium.com/sprinthub/creating-a-customized-tab-bar-in-ios-with-swift-41ed380f2a30 and github: https://github.com/quacklabs/customTabBarSwift)
- Awesome animated tabbar (uses stackviews) [https://medium.com/nickelfox/ios-swift-custom-tabbarcontroller-with-tab-item-animation-b26f6a6e2c62](https://medium.com/nickelfox/ios-swift-custom-tabbarcontroller-with-tab-item-animation-b26f6a6e2c62)
- UICollectionView based TabBar (uses ViewModel, bit too elaborate ☠️): [https://betterprogramming.pub/how-to-create-a-custom-tabbar-in-swift-d44b3db3ac0e and github: https://github.com/cipolleschi/CustomTabBar](https://betterprogramming.pub/how-to-create-a-custom-tabbar-in-swift-d44b3db3ac0e and github: https://github.com/cipolleschi/CustomTabBar)
- Overrides Native tabbar: [https://github.com/Ramotion/adaptive-tab-bar](https://github.com/Ramotion/adaptive-tab-bar) animated: [https://github.com/Ramotion/animated-tab-bar](https://github.com/Ramotion/animated-tab-bar)
- Advance anim over native tabbar: [https://github.com/softhausHQ/CircleBar](https://github.com/softhausHQ/CircleBar) and [https://github.com/Yalantis/ColorMatchTabs](https://github.com/Yalantis/ColorMatchTabs)
- Fancy tabbar: [https://github.com/Ahmadalsofi/SOTabBar](https://github.com/Ahmadalsofi/SOTabBar)

### Custom TabBar Scope:
- background colors change on tab switch
- tabbar-item should have icon and textfield
- should have simple selected / unselected animation ✨
- tabbar-item should have background highlight with rounded corners
- tabbar-item bacgrkund should size it self based on text-size and padding
- tabbar should be responsible for laying out items, and support different aligning types (spatial)

### Customizing UITabBar
```swift
// How to set a custom tabBar to my custom TabBarController?
class MyCustomTabBarController: UITabBarController {
	override func viewDidLoad() {
        super.viewDidLoad()
        setValue(TBar(frame: tabBar.frame), forKey: "tabBar")
        view.backgroundColor = .white
        createShape()
    }
}
```

```swift
// substitute-the-uitabbar-of-uitabbarcontrolle
class CustomTabBarController: UITabBarController {
    let customTabBar = CustomTabBar()
    override var tabBar: UITabBar {
        return customTabBar
    }
}
```

### Frameworks:
- Comprehensive, overrides native UITabBar etc (messy code): https://github.com/eggswift/ESTabBarController


### API ideas:
```swift
var tabBarHeight: CGFloat = 67.0
enum TabItem: String, CaseIterable {
	case calls, photos, contacts
	 var vc: UIViewController {
	   switch self {
	   case .calls: return CallsViewController()
	   case .contacts: return ContactsViewController()
	   case .photos: return PhotosViewController()
	   }
	}
    var icon: UIImage { // these can be your icons
		 switch self {
		   case .calls: return UIImage(named: "ic_phone")!
		   case .contacts: return UIImage(named: "ic_camera")!
		   case .photos: return UIImage(named: "ic_contacts")!
	   }
    }
	 var displayTitle: String { self.rawValue.capitalized(with: nil) }
}
public enum Positioning {
    case automatic, fill, centered, fillExcludeSeparator, fillIncludeSeparator
}
// more here: https://github.com/eggswift/ESTabBarController/blob/master/Sources/ESTabBarItem.swift
open class CustomTabBarItem: UITabBarItem {
    open override var tag: Int { // The receiver’s tag, an application-supplied integer that you can use to identify bar item objects in your application. default is `0`
        didSet { self.contentView.tag = tag }
    }
}
// Great custom badge code: https://github.com/eggswift/ESTabBarController/blob/master/Sources/ESTabBarItemBadgeView.swift

// Properties to have:
// textColor
// highlightTextColor
// iconColor
// highlightIconColor
// backgroundColor
// highlightBackdropColor
```

### Custom TabBar:
- Skeleton of our custom tab bar
```swift
import UIKit

class TabNavigationMenu: UIView {
var itemTapped: ((_ tab: Int) -> Void)?
    var activeItem: Int = 0

    override init(frame: CGRect) {
        super.init(frame: frame)
    }
required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
    }
convenience init(menuItems: [TabItem], frame: CGRect) {
        self.init(frame: frame)
        // ...
    }
func createTabItem(item: TabItem) {
        // ...
    }
@objc func handleTap(_ sender: UIGestureRecognizer) {
        // ...
    }
func switchTab(from: Int, to: Int) {
        // ...    
    }
func activateTab(tab: Int) {
        // ...
    }
func deactivateTab(tab: Int) {
        // ...
    }
}
```
### Custom UINavigationController
```swift
class NavigationMenuBaseController: UITabBarController {
    var customTabBar: TabNavigationMenu!
    var tabBarHeight: CGFloat = 67.0
	override func viewDidLoad() {
	        super.viewDidLoad()
	        self.loadTabBar()
	    }
	func loadTabBar() {
	        // We'll create and load our custom tab bar here
	    }
	func setupCustomTabMenu(_ menuItems: [TabItem], completion: @escaping ([UIViewController]) -> Void) {
	// handle creation of the tab bar and attach touch event listeners
	    }
	func changeTab(tab: Int) {
        self.selectedIndex = tab
    }
}
```
