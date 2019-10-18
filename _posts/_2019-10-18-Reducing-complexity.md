My notes on reducing complexity in swift architectures.<!--more-->The goal here is to reduce the amount of variables and methods in architectural heavy classes such as Controllers. For max [https://www.codebeat.co/](https://www.codebeat.co/) GPA, try to have less than 6 variables, and less than 10 or 20 methods in each class. But moving code to the view or the model doesn't reduce complexity it actually increases coupling, which makes the architectural classes harder to maintain and also increases  [cyclomatic complexity](https://en.wikipedia.org/wiki/Cyclomatic_complexity)  .

### Moving service/configurator/modifier/creator code to static methods in classes within classes
File naming scheme: `A.Service.swift` or `A.Configurator` etc
```swift
class A {}
extension A { class Service {} }
extension A.B {
  static func test(temp: String) { print("test: \(temp)") }
}
```

### Moving delegate code into extension in their own classes
File naming scheme: `A+UIScrollViewDelegate.swift` etc
```swift
class A {}
extension A: UIScrollViewDelegate {
  func scrollViewDidEnd() {
    //..
  }
}
```
