```swift
class Test {
  UIView.test() // works
  UIView.reload() // doesn't work
}
extension UIView {
  fileprivate static func reset() {
    print("rest")
  }
  private static func reload() {
    print("reload")
  }
}
```
