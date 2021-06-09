My notes on private vs fileprivate<!--more--> fileprivate is mostly not needed, because IMO. moving code to many files is better than storing alot of code in big files.


```swift
class Test {
  UIView.reset() // works (because in same file)
  UIView.reload() // doesn't work
}
extension UIView {
  fileprivate static func reset() {
    print("reset")
  }
  private static func reload() {
    print("reload")
  }
}
```
