My notes on the Swift Result type<!--more-->


```swift
 /**
  * Tests result type with try / throw oriented code
  */
 func testResult() {
    let result = Result { try testSomething(flag: false) } // Uhoh something went wrong
    switch result {
    case .success(let content):
       print("content.content: \(content.count)")
    case .failure(let error):
       print(error.localizedDescription)
    }
    // Alternativly:
    let anotherResult = Result { try testSomething(flag: true) } // It works 🎉
    if case .success(let count) = anotherResult {
       print("\(count) unread messages.")
    } else if case .failure(let error) = anotherResult {
       print(error.localizedDescription)
    }
    // I just want the value:
    let result1 = try? testSomething(flag: true)
    guard let theValue: String = result1.map({ "And the result is: \($0)" }) else { Swift.print("err"); return }
    Swift.print("theValue:  \(theValue)")
    // alternativly:
    let res1: String? = try? testSomething(flag: true).get()
    swift(res1) // It works 🎉
 }
 /**
  * Tests throwing
  */
 func testSomething(flag: Bool) throws -> String {
    if flag {
       return "It works 🎉"
    } else {
       throw NSError.init(domain: "Uhoh something went wrong 🚫", code: 0)
    }
 }
```

### Create typealias for more readable code:

```swift
typealias OnComplete = (Result<UIImage, Error>) -> Void

func process(_ image: UIImage, then completion: @escaping OnComplete) {
    do {
        var image = try transformer.transform(image)
        image = try filter.apply(to: image)
        completion(.success(image))
    } catch {
        completion(.failure(error))
    }
}
```

### Other:

There is also: mapError() and flatMapError(), they transform the error value rather than the success value.
