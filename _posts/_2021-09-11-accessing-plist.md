Notes on Plist<!--more-->

### Example of accessing plist:
```swift
if let path = NSBundle.mainBundle().pathForResource("Config", ofType: "plist") {
  if let dict = NSDictionary(contentsOfFile: path) as? Dictionary<String, AnyObject> {
    // use swift dictionary as normal
  }
}
```
