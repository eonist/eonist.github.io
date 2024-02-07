My notes on using swift-package-manager in swiftui<!--more-->


### Setup:
1. XCode -> File -> new package -> (create a new package in the root of your app folder)
2. XCode -> File -> add depedency pacakge -> press add local pacakge -> pick the new package
3. import YourNewPackage in files you need access to pacakge code
4. make things public to make the api available outside the package


### Example:

Package:
```swift
public struct DatabaseLib {
   // 
   public static func testing() {
      Swift.print("testing")
   }
}
```

Using the package:

```swift
import DatabaseLib

DatabaseLib.testing() // testing
```

### Gotchas
- XCode seems a bit unstable when opening new packages. Sometimes it requires closing xcode and dragging the package onto the xcode icon again to open it isolated. 