Notes made while researching various ways to do darkmode in iOS<!--more-->, in the end I built a small framework to help with configuring and using darkmode (for both iOS and macOS): [https://github.com/sentryco/DarkMode](https://github.com/sentryco/DarkMode)

### Color
```swift
import UIKit

public extension UIColor {
    /**
     * Creates a color object that generates its color data dynamically using the specified colors. For early SDKs creates light color.
     *
     * - Parameters:
     *   - light: The color for light mode.
     *   - dark: The color for dark mode.
     */
    convenience init(light: UIColor, dark: UIColor) {
        if #available(iOS 13.0, tvOS 13.0, *) {
            self.init { traitCollection in
                if traitCollection.userInterfaceStyle == .dark {
                    return dark
                }
                return light
            }
        }
        else {
            self.init(cgColor: light.cgColor)
        }
    }
}
```

### Auto colors:
```swift
let view = UIView()
view.backdroundColor = .systemRed
```

### For layers:
```swift
override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
    super.traitCollectionDidChange(previousTraitCollection)
    traitCollection.hasDifferentColorAppearance(comparedTo: traitCollection) {
        layer.backgroundColor = UIColor.layer.cgColor
    }
}
```

### Debugging:
If you are debugging one of the color schemes, it’s handy to fix dark appearance in the Simulator in Preferences > Developer > Dark

### Using catalog asset to store colors
- The benefit is that you can create dark and light modes
- If your app uses either Storyboards or XIBs, the best approach is to use color assets.

`let background = UIColor(named: "background")`

```swift
enum AssetsColor : String {  
  case background  
  case title  
  case subTitle  
  case tabBar
}
extension UIColor {
  static func appColor(_ name: AssetsColor) -> UIColor? {
     return UIColor(named: name.rawValue)
  }
}
```
`self.view.layer.shadowColor = UIColor.appColor(.shadow)`

```swift
if traitCollection.userInterfaceStyle == .dark{
   self.layer.shadowOpacity = 0.0;
}
else{
   self.layer.shadowOpacity = 1.0;
}
```

### Changing the tint on image assets:
```swift
let imageIcon   = UIImage()
let iconImgView = UIImageView()
//Apply your rendering mode to the image
imageView.image = imageIcon.withRenderingMode(.alwaysTemplate)
//And change its tint color here
imageView.tintColor = UIColor.appColor(.label)
```


### Hex color
```swift
import UIKit

extension UIColor {
    static var customAccent: UIColor { return MaterialUI.red500 }
    …
}
fileprivate enum MaterialUI {
    static let orange600 = UIColor(red:   0xFB / 0xFF,
                                   green: 0x8C / 0xFF,
                                   blue:  0x00 / 0xFF,
                                   alpha: 1) // #FB8C00
    …
}
```

### Toggle colors based on darkmode / lightmode:

```swift
import UIKit

extension UIColor
    static var customAccent: UIColor {
        if #available(iOS 13, *) {
            return UIColor { (traitCollection: UITraitCollection) -> UIColor in
                if traitCollection.userInterfaceStyle == .dark {
                    return MaterialUI.orange300
                } else {
                    return MaterialUI.orange600
                }
            }
        } else {
            return MaterialUI.orange600
        }
    }
}
```

### Another:

```swift
let lightColor = // your custom light color
let darkColor = // your custom dark color
var dynamicColor: UIColor {
    // If you support earlier versions, then check
    // the version before providing dynamic colors…
    if #available(iOS 13.0, *) {
        return UIColor {
            (traitCollection: UITraitCollection) -> UIColor in
                switch traitCollection.userInterfaceStyle {
                case .dark:
                    return darkColor
                default:
                    return lightColor
                }
        }

    // For older versions, just return the standard color
    } else {
        return lightColor
    }
}
```

### React to dark mode change:

```swift
class MyViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        if self.traitCollection.userInterfaceStyle == .dark {
            // User Interface is Dark
        } else {
            // User Interface is Light
        }
    }
    override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
        // Trait collection has already changed
		  super.traitCollectionDidChange(previousTraitCollection)
		  // traitCollectionDidChange also gets called a few times. This is how I detect DarkMode runtime change and setColors() only once.
		  guard UIApplication.shared.applicationState == .inactive else { return }

   let userInterfaceStyle = traitCollection.userInterfaceStyle // Either .unspecified, .light, or .dark
   // Update your user interface based on the appearance
    }
    override func willTransition(to newCollection: UITraitCollection, with coordinator: UIViewControllerTransitionCoordinator) {
        // Trait collection will change. Use this one so you know what the state is changing to.
    }
}
```


### Reactive but only changes if prev is diff:

```swift
override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
  super.traitCollectionDidChange(previousTraitCollection)
  if #available(iOS 13.0, *) {
      if self.traitCollection.hasDifferentColorAppearance(comparedTo: previousTraitCollection) {
          if traitCollection.userInterfaceStyle == .dark {
              //Dark
          }
          else {
              //Light
          }
      }
  } else {
      // Fallback on earlier versions
  }
}
```
### Reactive but simpler
```swift
override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
    super.traitCollectionDidChange(previousTraitCollection)
    guard previousTraitCollection?.userInterfaceStyle != traitCollection.userInterfaceStyle else {
        return
    }
    dropShadowIfNeeded()
}
```
### Handy extension:

```swift
extension UIViewController {
    var isDarkMode: Bool {
        if #available(iOS 13.0, *) {
            return self.traitCollection.userInterfaceStyle == .dark
        } else {
            return false
        }
    }
}
enum ColorCompatibility {
    static var myOlderiOSCompatibleColorName: UIColor {
        if UIViewController().isDarkMode {
            return UIColor(red: 33, green: 35, blue: 37, alpha: 0.85)
        }
        else {
            return UIColor(hexString: "#F3F3F3", alpha: 0.85)
        }
    }
}
```

### Reactive colors:
That's it. No need to define two separate statics. The control class doesn't need any changes from the original code. No need to override traitCollectionDidChange or anything else.
The nice thing about this is that you can see the color change in the app switcher immediately after changing the mode in the Settings app. And of course the color is up-to-date automatically when you go back to the app.

```swift
//UIColor init(dynamicProvider:) initializer.
UIColor.init { (trait) -> UIColor in
    return trait.userInterfaceStyle == .dark ? .label : .black
}
```



```swift
struct MyColors {
   ///> This is what you are getting from designers,
   /// in case they are not providing consistent color naming.
   /// Can be also just strings with HEX-codes.
   static let xF9EFB1 = #colorLiteral(red: 0.9764705882352941, green: 0.9372549019607843, blue: 0.6941176470588235, alpha: 1)
   static let x6A6A6A = #colorLiteral(red: 0.4156862745098039, green: 0.4156862745098039, blue: 0.4156862745098039, alpha: 1)
   static let xFEFEFE = #colorLiteral(red: 0.9960784313725490, green: 0.9960784313725490, blue: 0.9960784313725490, alpha: 1)
   static let x202020 = #colorLiteral(red: 0.1254901960784314, green: 0.1254901960784314, blue: 0.1254901960784314, alpha: 1)
   ///<
   static var myLabelForeground: UIColor {
      return UIColor.dynamicColor(light: MyColors.x6A6A6A, dark: MyColors.xF9EFB1)
   }
   static var myViewBackground: UIColor {
      return UIColor.dynamicColor(light: MyColors.xFEFEFE, dark: MyColors.x202020)
   }
}
```

### For macOS:

```swift
import AppKit

extension NSColor {
   public class func dynamicColor(light: NSColor, dark: NSColor) -> NSColor {
      if #available(OSX 10.15, *) {
         return NSColor(name: nil) {
            switch $0.name {
            case .darkAqua, .vibrantDark, .accessibilityHighContrastDarkAqua, .accessibilityHighContrastVibrantDark:
               return dark
            default:
               return light
            }
         }
      } else {
        return light
      }
   }
}
```

### For cells:

```swift
// Cell will detect, layer will not! You must manually update all layer adaptations in the cell for example.

override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
    super.traitCollectionDidChange(previousTraitCollection)

    if traitCollection.hasDifferentColorAppearance(comparedTo: previousTraitCollection) {
        removeAndReaddGradientIfNeeded()
    }
}
```


### Supporting High contrast:
This structure can maybe be simplified by putting the enum inside UITraitCollection?
```swift
public extension UIColor {
   /**
    * ## Examples:
    * let customColor = UIColor.customColor
    * print(customColor) 🏀
    */
    private static func make(dynamicProvider: @escaping (Theme) -> UIColor) -> UIColor {
        guard #available(iOSApplicationExtension 13.0, *) else { return dynamicProvider(.light) }
        return UIColor { (traitCollection) -> UIColor in
            return dynamicProvider(Theme(traitCollection))
        }
    }
    /**
     * Custom color
     */
    static var customColor: UIColor {
        return .make { (theme) -> UIColor in
            switch theme {
            case .light: return .somethingCustom1
            case .dark: return .somethingCustom2
            case .lightHighContrast: return .somethingCustom3
            case .darkHighContrast:  return .somethingCustom4
            }
        }
    }
}

public enum Theme {
    case light, dark, lightHighContrast, darkHighContrast
}
extension Theme {
    @available(iOSApplicationExtension 13.0, *)
    init(_ traitCollection: UITraitCollection) {
        switch (traitCollection.userInterfaceStyle, traitCollection.accessibilityContrast) {
        case (.dark, .high):
            self = .darkHighContrast

        case (.dark, _):
            self = .dark

        case (_, .high):
            self = .lightHighContrast

        default:
            self = .light
        }
    }
}
```


### Sematic color name suggestions:

```swift
//Label Colors
label
secondaryLabel
tertiaryLabel
quaternaryLabel
//Text Colors
placeholderText
Link Colors
link
//Separator Colors
separator
opaqueSeparator
//Fill Colors
systemFill
secondarySystemFill
tertiarySystemFill
quaternarySystemFill
//Background Colors
systemBackground
secondarySystemBackground
tertiarySystemBackground
//Grouped Background Colors
systemGroupedBackground
secondarySystemGroupedBackground
tertiarySystemGroupedBackground
```

### You can override the interface style of UIViewController by
1: overrideUserInterfaceStyle = .dark //For dark mode
2: overrideUserInterfaceStyle = .light //For light mode
```swift
class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        overrideUserInterfaceStyle = .light    
    }
}
```

### A way to toggle images:

```swift
import UIKit

public class MixedResource<T> {
    public let normalResource: T
    public let nightResource: T
    public init(normal: T, night: T) {
        normalResource = normal
        nightResource = night
    }

    public func unfold() -> T {
        switch NightNight.theme {
        case .normal: return normalResource
        case .night:  return nightResource
        }
    }
}

public class MixedImage: MixedResource<UIImage> {
    public override init(normal: UIImage, night: UIImage) {
        super.init(normal: normal, night: night)
    }
    public convenience init(normal: String, night: String) {
        self.init(normal: UIImage(named: normal)!, night: UIImage(named: night)!)
    }
}

public class MixedColor: MixedResource<UIColor> {
    public override init(normal: UIColor, night: UIColor) {
        super.init(normal: normal, night: night)
    }
    public init(normal: Int, night: Int) {
        let normalColor = UIColor(rgb: normal)
        let nightColor = UIColor(rgb: night)
        super.init(normal: normalColor, night: nightColor)
    }
}

public class MixedStatusBarStyle: MixedResource<UIStatusBarStyle> {
    public override init(normal: UIStatusBarStyle, night: UIStatusBarStyle) {
        super.init(normal: normal, night: night)
    }
}

public class MixedBarStyle: MixedResource<UIBarStyle> {
    public override init(normal: UIBarStyle, night: UIBarStyle) {
        super.init(normal: normal, night: night)
    }
}

public class MixedKeyboardAppearance: MixedResource<UIKeyboardAppearance> {
    public override init(normal: UIKeyboardAppearance, night: UIKeyboardAppearance) {
        super.init(normal: normal, night: night)
    }
}
```

# Second tip: Adding a key in info.plist
Simply you can add a new key `UIUserInterfaceStyle` in your app info.plist and set its value to Light or Dark. this will override the app default style to the value you provide.
You don't have to add overrideUserInterfaceStyle = .light this line in every viewController, just one line in info.plist that’s it.

### Resources:
- Store theme in userdefaults: https://medium.com/@drevathy/custom-themes-with-color-assets-in-swift-9e64f91ee45d
- Might have a way to toggle live: https://github.com/draveness/NightNight/
- Comprehnsive tutorial: https://www.gurutechnolabs.com/ios-13-dark-mode/

### Good to know:
- To change app dark mode programatically: `window.overrideUserInterfaceStyle = .dark`
- To change controller dark mode programatically: `self.overrideUserInterfaceStyle`
- You can toggle dark / light in simulator: When you launch simulator, an environment icon override icon will appear on the console bar
