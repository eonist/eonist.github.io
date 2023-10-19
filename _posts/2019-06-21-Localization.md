My notes on adding localization to an iOS app<!--more-->

## Setup:
1. Create the Localization.strings file from file -> strings file
2. Click the .strings file and in the right pan, click localize
3. Click the .strings file again and you can now add sub-languages
4. In scehme, add spanish etc.
5. Add language content, see the format paragraph:

## Format:
In the sub .strings file name ...spanish.strings add:

```
"Freedom" = "Libre";
"The world" = "El mundo";
```

### Example:
```swift
/**
 * Localization
 */
extension MainTabBarController {
   /**
    * Testing loalization
    * ## Examples:
    * Swift.print("testTitle:  \(NSLocalizedString("Bookmarks", comment: ""))")
    */
   func testLocalization() {
      let preferredLanguage = NSLocale.preferredLanguages[0]
      if preferredLanguage == "en" {
         print("👌 this is English")
      } else if NSLocale.preferredLanguages[0].range(of: "es") != nil {
         print("👌 this is spanish")
      }
   }
}
```
