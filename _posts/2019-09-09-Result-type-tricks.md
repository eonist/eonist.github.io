My notes on the Swift Result type<!--more-->

### Simple Result:
- Result is great with callbacks
- Result is not great for returning values, use throw instead

### Simple Result
```swift
typealias Complete = (Result<UIImage, Error>) -> Void

func getSomething(onComplete: Complete) {
   guard let img: UIImage = image else { onComplete(.failure(NSError.init(domain: "err", code: 0))); return }
   onComplete(.success(hccqrImage))
}

getSomething { result in
   guard let img = try? result.get() else { print("error") }
   _ = img
}
```

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
    // Alternative:
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

### Create TypeAlias for more readable code:

```swift
typealias OnComplete = (Result<UIImage, Error>) -> Void

func process(_ image: UIImage, then completion: @escaping OnComplete) {
    do {
        var image = try transformer.transform(image)
        image = try filter.apply(to: image)
        completion(.success(image))
    } catch let error {
        completion(.failure(error))
    }
}
```

### Use guard with result:

```swift
enum NetworkResponse {
  case response(URLResponse, Data)
  case error(Error)
}
processRequestResponse {
   guard case .success(let value) = $0 else { return } // you can also do the same for .failure
   print(value)
   //continue making other async requests
}
```

### Adding a custom error to the result:

```swift
/**
 * Errors
 */
extension CamUtil {
   public enum MicAccessError: Error {
      case authorized, deniedOrRestricted, notDetermined
   }
}
```

### Transforming the error into another:

```swift
let customErrorResult = $0.flatMapError { (error) -> Result<Void, CamUtil.MicAndVideoAccessError> in
   switch error {
   case .micAccessNotDetermined:
      return Result.failure(.micAccessNotDetermined)
   case .micAuthorized, .micDeniedOrRestricted, .videoAccessWasDenied:
      return Result.failure(.videoAccessWasDenied)
   }
}
```

### Casting a throwing method to a Result:
```swift
let result = Result { try String(contentsOfFile: someFile) }
```

### Using result extensions for Result:
```swift
extension Result {
   /**
    * - Note: Sometimes you just want to do print($0.errorStr)
    */
   public var errorStr: String {
      guard let error = self.error else { return "success" }
      return error.localizedDescription
   }
}

enum SomeError: Error {
   case a, b, c
}

func fetchValue(_ flag: Bool) -> Result<Int, SomeError> {
   if flag {
      return .success(1)
   } else {
      Swift.print("before")
      let temp: Result<Int, SomeError> = .failure(.b)
      Swift.print("after")
      return temp
   }
}

func testing(_ flag: Bool) -> Int? {
   let result = fetchValue(flag)
   guard case .success(let value) = result else { let errStr = result.error; Swift.print("errStr:  \(errStr)"); return nil }
   return value
}

Swift.print("testing(true):  \(testing(true))")
Swift.print("testing(false):  \(testing(false))")
```


### Access value directly:

```swift
/**
 * ## Examples
 * guard let imageAndURL: (UIImage, URL) = $0.value else { return }
 * imageAndURL.image // UIImage
 * imageAndURL.url // URL
 */
public func value<T>() -> T? {
   return try? self.get() as? T
}
```
### Other:

There is also: mapError() and flatMapError(), they transform the error value rather than the success value.


### Simple result:

```swift
func divide(_ x: Int, by y: Int) -> Result<Int, DivisionError> {
    guard y != 0 else {
        return .failure(.zeroDivisor)
    }
    return .success(x / y)
}

let result = divide(10, by: 2)
switch result {
case .success(let number):
    print(number)
case .failure(let error):
    print(error.localizedDescription)
}
```


### One-liner:

```swift
func test(result: Result<CVImageBuffer, Error>) {
   guard let imageBuffer: CVImageBuffer = try? result.get() else { if case .failure(let error) = result { Swift.print("error:  \(error)") }; return }
   //read imageBuffer
}
```

### Result with out a value

```swift
// Result type without value to provide

// Result type usage is really popular nowadays.

enum Result<T> {
    case success(result: T)
    case failure(error: Error)
}

func login(with credentials: Credentials, handler: @escaping (_ result: Result<User>) -> Void) {
    // Two possible options:
    handler(Result.success(result: user))
    handler(Result.failure(error: UserError.notFound))
}
```

login(with:) operation has user value to provide and default Result type fits perfectly here. But let’s imagine that your operation hasn’t got value to provide or you don’t care about it. Default Result type makes you to provide the result value any way.

To fix this inconvenience you need to add extension and instantiate a generic with an associated value of type Void.

`func login(with credentials: Credentials, handler: @escaping (_ result: Result<Void>) -> Void)`

```swift
extension Result where T == Void {
    static var success: Result {
        return .success(result: ())
    }
}
```
Now we can change our func login(with:) a bit, to ignore result success value if we don’t care about it.

```swift
func login(with credentials: Credentials, handler: @escaping (_ result: Result<Void>) -> Void) {
    // Two possible options:
    handler(Result.success)
    handler(Result.failure(error: UserError.notFound))
}
```

### Resources:
- [https://github.com/eonist/ResultSugar](https://github.com/eonist/ResultSugar) 