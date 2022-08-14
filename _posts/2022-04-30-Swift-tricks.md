singletonSome of my favourite swift tricks<!--more-->

## 91. Customizable singleton
- Normal usage in production code: `SomeClass.shared` // instance   
- Test usage: `SomeClass.sharedInstance(isTestMode: true)` // test instance
```swift
public static let shared: SomeClass = .sharedInstance() // with default customization
   private static var _shared: SomeClass? // One source of truth
   /**
    * - Note: Use this directly if this singleton needs to be customized (Call it only once in the beginning of the code, use regular .shared in subsequent calls etc)
    * - Important: ⚠️️ Reference this in the code before .shared is referenced or it wont work
    */
   public static func sharedInstance(isTestMode: Bool = false) -> SomeClass {
      guard let shared = _shared else { // if nil -> first run
         let shared = SomeClass(isTestMode: isTestMode) // temp variable
         _shared = shared // set permanent variable
         return shared
      }
      return shared // Instance  already exist, return instance
   }
```

## 90. Handy way to generate uuid's
In terminal we can do: `uuidgen` output: `1745B9C9-A369-4FD7-1EDF-B3AE2C268047`
```swift
let uuid = UUID().uuidString // 2C432AEC-14A9-4F83-9ABB-60C6A7D948E7
```

## 89. Manipulating values in a Dictionary
 Manipulate key and value
```swift
let newDict = Dictionary(uniqueKeysWithValues: oldDict.map { key, value in (key.uppercased(), value.lowercased()) })
```

## 88. Debugging main / background thread
Great when working with network-IO and UI, since UI must happen on main thread and network usually on background.
```swift
print("Thread.isMainThread: \(Thread.isMainThread)") // true / false
```

## 87. Weak self in a method
Weak self can be used in methods, not just closures
```swift
func myInstanceMethod() {
    weak var _self = self
    func nestedFunction(result : Bool) {
        _self?.anotherInstanceMethod()
    }
    functionExpectingClosure(nestedFunction)
}
```

## 86. Intersection of two arrays
We can also use Set to achieve intersection between arrays see stackoverflow
```swift
func intersection(a: [String], b: [String]) -> [String] {
   a.filter { character in
      b.contains(where: { character == $0 })
   }
}
```

## 85. Use deinit to clean up or cancel callbacks
```swift
class Test {
    deinit {
        print("deinit")
        // cancel callbacks etc
    }
}
var test: Test? = Test()
test = nil // prints deinit
```

## 84. Compare equality between two instance of type Any
```swift
/**
 * A hack to compare any
 */
fileprivate extension Equatable {
   /**
    * Equate two values of unknown type.
    * ## Examples
    * let bool: Any = Bool.random()
    * AnyHashable.equate(bool, bool) // true
    */
   static func equate(_ any0: Any?, _ any1: Any?) -> Bool {
      if any0 == nil && any1 == nil { return true }
      guard
         let equatable0 = any0 as? Self,
         let equatable1 = any1 as? Self
      else { return false }

      return equatable0 == equatable1
   }
}
```

## 83. Forced unwrap with context
istead of unwrapping and getting no useful info if the value is nil, use fatalError instead.
```swift
let someValue: Int? = 0
let value = someValue ?? { fatalError("Integer missing") }()
```

## 82. Simpler error for Result
Sometimes you just want to read the error instead of switching
```swift
extension Result {
   public func error<T>() -> T? where T: Error {
      guard case .failure(let error) = self else { return nil }
      return error as? T
   }
}
```
```swift
enum DataReceivedError: Error { case offline, denied }
typealias OnDataReceived = (Result<String, DataReceivedError>) -> Void
var onDataReceived: OnDataReceived = { result in
   guard value = try? result.get() else { print("Err: \(result.error?.localizedDescription)") }
   print("content: \(value)")
}
onDataReceived(.success("some content"), nil) // success
onDataReceived(.failure(.offline)) // failure
```

## 81. Handy UIAlertController shortcuts:
Simplifies calling UIAllertController
```swift
// Extension:
extension UIAlertController {
    static func alertOnError(_ error: Swift.Error, handler: ((UIAlertAction?) -> Void)? = nil) -> UIAlertController {
        let alert = UIAlertController(title: "Error", message: error.localizedDescription, preferredStyle: UIAlertController.Style.alert)
        alert.addAction(UIAlertAction(title: "OK", style: UIAlertAction.Style.default, handler: handler))
        return alert
    }
    static func alertOnErrorWithMessage(_ message: String, handler: ((UIAlertAction?) -> Void)? = nil) -> UIAlertController {
        let alert = UIAlertController(title: "Error", message: message, preferredStyle: UIAlertController.Style.alert)
        alert.addAction(UIAlertAction(title: "OK", style: UIAlertAction.Style.default, handler:handler))
        return alert
    }
    static func alertWithMessage(_ message: String, handler: ((UIAlertAction?) -> Void)? = nil) -> UIAlertController {
        let alert = UIAlertController(title: "Alert", message:message, preferredStyle: UIAlertController.Style.alert)
        alert.addAction(UIAlertAction(title: "OK", style: UIAlertAction.Style.default, handler:handler))
        return alert
    }
}
// Usage:
let err = NSError(domain: "err", code: 0)
let closure = { (_ action: UIAlertAction?) in print(action) }
UIAlertController.alertOnError(err, closure).present()
UIAlertController.alertOnErrorWithMessage("Uh oh", closure).present()
UIAlertController.alertWithMessage("Open the bay doors?", closure).present()
```

## 80. Overriding computed variable in an extension
It's nice to be able to put non stored variables in extensions
```swift
class A {}
extension A {
   @objc var data: String { "a" }
}
class B: A {}
extension B {
   override var data: String { "b" }
}
print(A().data) // a
print(B().data) // b
```
## 79. guard continue in a for loop
```swift
for item in items {
   guard item.uuid == uuid else { continue } // skip this loop iteration
   print("found matching uuid: \(item.uuid)")
   guard item.data.count > 0 { continue } // skip items that doesn't have data
   print("data.count: \(data.count)")
}
```

## 78. guard continue
We mostly use guard return. But there is also guard continue.

```swift
let str = "abc"
guard str == "abc" else { print("str is not abc"); continue }
```

## 77. Disabling some code for swift lint
Instead of disabling swift lint rules to avoid excessive xCode warnings you can disable small parts of the code:

```swift
// swiftlint:disable pattern_matching_keywords
case .detail(let acc, let mode): gotTo(view: view, account: acc, mode: mode)
// swiftlint:enable pattern_matching_keywords
```

## 76. Traversing descendants

```swift
/**
 * Traverses the entire UIView hierarchy downwards and collects views that are of specific PARAM: type
 */
public func descendants<T>(type: T.Type? = nil) -> [T] {
   self.subviews.flatMap { $0.subviews.isEmpty ? [$0 as? T].compactMap { $0 } : $0.descendants(type: type) }
}
```

## 75. Guard that throws
Nice way to provide info regarding why a function didn't return the expected result etc

```swift
func someFunc(toggle: Boolean) -> String throws  {
   guard toggle else { throw NSError(domain: "err", code: 0) }
   return "someString"
}
try ? someFunc(toggle: true) // "someString"
try ? someFunc(toggle: false) // nil
```

## 74. Array and Identifiable:
```Swift
public extension Array where Element: Identifiable {
    func find(_ id: Element.ID) -> Element? {
        self.first { $0.id == id }
    }
    func findIndex(_ id: Element.ID) -> Int? {
        self.firstIndex { $0.id == id }
    }
    mutating func remove(_ id: Element.ID) {
        self.removeAll { $0.id == id }
    }
}
```

## 73. Switch on instance type:
Nice way to switch on types etc.
```swift
class A {}
class B {}

let val: Any = B()
switch val {
  case is A:
    print("A")
  case is B:
    print("B")
  default:
    print("nothing")
}
// B
```


## 72. Struct and dictionary conversion:
```swift
extension Encodable {
   public var dict: [String: Any]? {
      guard let data = try? JSONEncoder().encode(self) else { return nil }
      return (try? JSONSerialization.jsonObject(with: data, options: .allowFragments)).flatMap { $0 as? [String: Any] }
   }
   public init(dict: [String: Any]) throws {
      self = try JSONDecoder().decode(Self.self, from: JSONSerialization.data(withJSONObject: dict))
   }
}
struct Job: Codable { let number: Int, name: String, client: String }
let job: Job = .init(number: 1234, name: "Awards Ceremony", client: "ACME Productions")
let dict: [String: Any] = job.dict ?? [:]
let clone = try? Job(dict: dict)
print("\(job == clone ? "✅" : "🚫")") // ✅
```

## 71. Setting a value with a key
```swift
class MyObject: NSObject{
    @objc public var myString : String = "Not working"
}
func test(){
    let value = "It works!"
    let member = "myString"
    let myObject = MyObject()
    myObject.setValue(value, forKey: member)
    print("New value: \(myObject.myString)")
}
```

### 70. Loop through struct with reflection
```swift
/**
* Creates dictionary of struct
* - Parameter instance: instance of struct
* - Returns: Dictionary with key value
*/
static func dict<T>(instance: T) -> [String: Any] {
   let mirror = Mirror(reflecting: instance)
   let keysWithValues = mirror.children.compactMap { (label: String?, value: Any) -> (String, Any)? in
      guard let label = label else { return nil }
      return (label, value)
   }
   return Dictionary(uniqueKeysWithValues: keysWithValues)
}
```

### 69. Loop through struct / class properties:
```swift
extension Encodable {
    var dictionary: [String: Any]? {
        guard let data = try? JSONEncoder().encode(self) else { return nil }
        return (try? JSONSerialization.jsonObject(with: data, options: .allowFragments)).flatMap { $0 as? [String: Any] }
    }
}
struct Test: Encodable {
    let title: String = "this is title"
    let value: Int = 3
}
let test = Test()
test.dictionary?.forEach { (key, value) in print("key: \(key) value: \(value)") }
// key: value value: 3
// key: title value: this is title
```

### 68. Sort with a more elaborate closure:
```swift
let closure: (_ a: Account, _ b: Account) -> Bool = { a, b in
   if let a = a as? LoginItem, let b = b as? LoginItem {
      return a.type < b.type
   } else {
      return a.date < b.date
   }
}
return accounts.sorted(by: closure)
```

### 67. Find spm .build files
These folders are hidden, and adds size to projects. Find them with terminal. Then delete them
```
find . -name ".build"
```


### 66. Check equality between Arrays
This works when order of the array doesn't matter
```swift
func isEqual(a: [String], b: [String]) -> Bool {
   guard a.count == b.count else { return false } // Check if count is the same
   return !a.contains { item in // Does it have a case where item isn't found in the other array
      !b.contains { otherItem in // Does it have a case where item isn't found in the other array
         item != otherItem
      }
   }
}
```

### 65: Operate on an array of class types
This can be useful for registering cell classes for instance
```swift
protocol Kind: AnyObject {}
class A: Kind {}
class B: Kind {}
extension Array where Element == Kind.Type {
   func read() {
      self.forEach { Swift.print("$0: \($0)") }
   }
}
[A.self, B.self].read() // A, B
```
`

### 64: Make a struct mutable
```swift
struct X {
   let a: String, b: Bool, c: Int
   init(a: String, b: Bool, c: Int) {
      self.a = a
      self.b = b
      self.c = c
   }
}
extension X {
   init(x: X, a: String? = nil, b: Bool? = nil, c: Int? = nil) { // the x contains the default values
      self.a = a ?? x.a
      self.b = b ?? x.b
      self.c = c ?? x.c
   }
}
let x1: X = .init(a: "test", b: true, c: 4)
Swift.print("x1:  \(x1)") // X(a: "test", b: true, c: 4)
let x2: X = .init(x: x1, a: "lol")
Swift.print("x2:  \(x2)") // X(a: "lol", b: true, c: 4)
let x3: X = .init(x: x2, b: false)
Swift.print("x3:  \(x3)") // X(a: "lol", b: false, c: 4)
```

### 63: Insert after match
```swift
extension Collection {
   /**
    * ## Examples:
    * let arr = ["a", "g", "r"]
    * let idx = arr.insertionIndex(of: "m", using: <)
    * rowData.insert("m", at: idx) // ["a", "g", "m", "r"]
    */
   func insertionIndex(of element: Self.Iterator.Element, using areInIncreasingOrder: (Self.Iterator.Element, Self.Iterator.Element) -> Bool) -> Index {
      firstIndex { !areInIncreasingOrder($0, element) } ?? endIndex
   }
}
```

### 62: Remove duplicates from the array, preserving the items order
```swift
extension Array where Element: Hashable {
    func filterDuplicates() -> Array<Element> {
        var set = Set<Element>()
        var filteredArray = Array<Element>()
        for item in self {
            if set.insert(item).inserted {
                filteredArray.append(item)
            }
        }
        return filteredArray
    }
}
```

### 61: Getting indices of filtered array
```swift
extension Array where Element: Equatable {
   /**
    * items.indexes(of: "A") // [0, 2, 4]
    * items.indexes(of: "B") // [1]
    */
    func indexes(of element: Element) -> [Int] {
        return self.enumerated().filter({ element == $0.element }).map({ $0.offset })
    }
}
```

### 60: Performance testing:
```swift
/**
 * - Note: Set a NSDate to measure the time like this: var startTime:NSDate = NSDate();abs(startTime!.timeIntervalSinceNow)
 * - Note: Or even easier: let d = CACurrentMediaTime(); /*Do heavy computing*/let d1 = CACurrentMediaTime()-d; print(d1)//0.452 sec
 * ## Examples:
 * testPerformance("Adding styles took: "){CSSFileParser.cssString(url)}//Adding styles took 2.4secs
 * - Parameter startTime: performance test start time
 */
func testPerformance(_ context: String = "", _ startTime: Date = Date(), _ closure:@escaping ()->Void) {
    closure()/*Executes the closure*/
    Swift.print(context + " \(abs(startTime.timeIntervalSinceNow))" + " Secs")/*Prints performance test end time*/
}
/**
 * Allows you to test performance and also return a value
 * ## Examples:
 * let result:String = testPerformance{readData()}//0.028 Secs
 */
func testPerformance<T>(_ context: String = "", _ startTime:Date = Date(), _ closure:@escaping ()->T) -> T {
    defer {/*execute just before code execution leaves the current block of code*/
        Swift.print(context + " \(abs(startTime.timeIntervalSinceNow))" + " Secs")/*Prints performance test end time*/
    }
    return closure()/*Executes the closure*/
}
```

### 59: Fitting a size to a ratio:
```swift
/**
 * Fits inside a frame (Scales to ratio)
 * - Fixme: ⚠️ Add zoom method: Always fills a frame, ️do this laster
 * - Note: Basically used to get a new size that fits inside size, and has the correct ratio
 * ## Examples:
 * fit(size: CGSize(width: 200, height: 200), ratio: 0.5) // CGSize(100, 200)
 */
static func fit(_ size: CGSize, ratio: CGFloat) -> CGSize{
    let w: CGFloat = size.width
    let h: CGFloat = size.height
    if (w / h) > ratio { // w is wider than ratio allows
        return CGSize(height: h * ratio, height: h)
    } else if (w / h) < ratio { // h is taller than ratio allows
        return CGSize(width: w, height: w * ratio)
    } else {
        return CGSize(width: w, height: h)
    }
}
```

### 58 For loops:

10++ different to for loop types:

- ``for i in 0..4 {}`` 👈 regular forward looping
- ``for (i, obj) in arr.enumerated() { print(i); print(obj) }`` 👈 access to i and obj
- ``for obj in arr {}`` 👈 iterate over objects (no access to original array)
- ``for i in (0..<4).reversed() {}`` 👈 backward looping
- ``var i = 0; while(i < 4) { print(i); i += 1 }`` 👈 If you want to manipulate i while looping
- ``for i in stride(from: 0, to: 10, skip: 2) {}`` 👈 Skips every other
- ``arr.forEach{$0}`` 👈 Easiest for-loop but only if you don't need to exit early
- ``for i in arr.indices {print(i)}`` 👈 Access to i
- ``for _ in 0..<arr.count`` 👈 If you just wan't to loop something and not use any value
- ``arr.reversed.forEach{$0}`` 👈 reversed forEach, more functional 🤖 .map also works
- `(0..<4).indices.map { i in return UIButton.init(frame: .zero) }` 👈 makes 4 buttons 🤖
- `for (i, str):(Int, String) in strings.enumerated() { print(("\(i) and \(str)")) }` 👈 special for loop
- `arr.enumerated().forEach { (_ i: Int,_ str: Data) in print(("\(i) and \(str)")) }` 👈 👌



### 57 EitherOr
By adding more .random calls you decrease the chance of being false.  
1/2, 1/4, 1/6, 1/8 etc
```swift
let eitherOr: Bool = !(Bool.random() && Bool.random()) // 1 in 4 is false
```


### 56 Autolayout across different subviews

Nice to know: If you ever want to use autolayout but need things to be above other elements etc. Autolayout works across subviews 👌


### 55 Better clamping:
```swift
/**
 * Clamp
 * ## Examples:
 * CGFloat.clamp(val: 44, min: 33, max: 38) // 38
 */
static func clamp(val: CGFloat, min: CGFloat, max: CGFloat) -> CGFloat {
   if val <= min { // under
      return min
   } else if val >= max { // over
      return max
   } else { // between
      return val
   }
}
```

### 54 Clamp a value between min and max
```swift
let value: CGFloat = 700
let lower: CGFloat = 200
let upper: CGFloat = 400
let clampedValue = max(lower, min(upper, value)) // 400
```

### 53 Reduce to total amount
```swift
let totalW: CGFloat = TabType.allCases.reduce(CGFloat(0)) {
   $0 + itemWidth(text: $1.rawValue)
}
```

### 52 Capitalize first letter:
```swift
extension String {
   /**
    * ## Examples:
    * let test = "the rain in Spain"
    * print(test.capFirst()) // "The rain in Spain"
    */
   var capFirst: String {
      prefix(1).capitalized + dropFirst()
   }
}
```

### 51 Fixed numbers:
```swift
let duration = String(format: "%.01f", 3.32323242) // 3.3
```

### 50 Simple diffing in swift:
```swift
let arrayA: [String] = ["a", "b", "c"]
let arrayB: [String] = ["c", "a", "d", "f", "g"]
// Find items in b that is not in a
let diff: [String] = arrayB.filter { item in
   !arrayA.contains { item == $0 }
}
Swift.print("diff:  \(diff)") // ["d", "f", "g"]
```

### 49: Init an enum
```swift
enum Apperance : String {
   case Dark, Light
}
extension Apperance {
   init() {
      let type = UserDefaults.standard.string(forKey: "AppleInterfaceStyle") ?? "Light"
      self = Apperance(rawValue: type)!
   }
   var inDarkMode: Bool {
      let currentStyle = Apperance()
      if case .Dark = currentStyle {
         return true
      } else if case .Light = currentStyle {
         return false
      } else {
         fatalError("Not supported")
      }
   }
}
```

### 48: Generic typealias
```swift
// Warning: ⚠️️ Generics does not work when overriding functions in extensions
typealias Parser<A> = (String) -> [(A, String)]
func parse<A>(stringToParse: String, parser: Parser)
```

### 46: Instead of opening playground. Use terminal to run some swift code
- All you do is open terminal. And write swift. copy paste any code and hit enter to run it.
```swift
while i < 4 {
   print("🎉")
   i += 1
}
```

### 45: Mark methods as deprecated and get warning:
- Document API changes with @available keyword.
- Great way to rename code without breaking backward compatibility, but at the same time motivating users to use the new api name, just make a typealias with the bellow code above it
```swift
@available(*, deprecated, renamed: "newMethodName")
func foo() {
  ...
}
// Each time foo() is called, a deprecation warning will appear:
// foo is deprecated, renamed `newMethodName`
// name things: allItems(SecClass:) if the type of the param is the only change etc
```

**There is also**:
```swift
@available(*, deprecated, message: "This closure will be removed in future version. Please use `handler`.")
```

### 44. Measure time consumed by a closure
```swift
/**
 * Measures how long a closure takes to complete
 * ## Examples:
 * timeElapsed { sleep(2.2) } // 2.20000
 */
func timeElapsed(_ closure: () -> Void) -> Double {
    let start = DispatchTime.now()
    closure()
    let end = DispatchTime.now()
    let diff = end.uptimeNanoseconds - start.uptimeNanoseconds
    return Double(diff) / 1_000_000_000
}
```

### 43. Do many things simultaneously and call onComplete when things are done
For more complex scenarios see: [https://github.com/eonist/parallelloops](https://github.com/eonist/parallelloops)
```swift
/**
 * - Abstract: process data in parallel on a background thread and calls a onComplete when it's complete
 * ## Examples:
 * processData { Swift.print("✅") } // Output: start, 1, 2, 0, 3, ✅
 */
func processData(onComplete: @escaping () -> Void) {
   Swift.print("start")
   DispatchQueue.global().async {
      DispatchQueue.concurrentPerform(iterations: 4) { index in
         Swift.print("\(index)")
         sleep((1..<3).randomElement()!) // Wait for n secs
      }
      onComplete()
   }
}
```

### 42. Map ranges:
Creates an array of random numbers

```swift
func randInt() -> Int { return Int(arc4random()) }
let randomArray = (1...4).map { _ in randInt() } // 3,1,2,2
```

### 41. Use an online swift playground for quick tests:
[http://online.swiftplayground.run](http://online.swiftplayground.run)

```swift
import Foundation
print("Hello World")
print((1...40).contains(1)) // true
print((1...40).contains(40)) // true
```

There is also terminal which can test swift code by typing swift and hitting enter

### 40. Flattening nested loops:

```swift
let width = 4
let height = 4
// nested loop
for y in (0..<height) {
   for x in (0..<width) {
      let index: Int = y * width + x
      print("index: \(index)")
   }
}
// flattening:
var x = 0
var y = 0
while x < width && y < height {
   let index: Int = y * width + x
   print("i: \(index)")
   x += 1
   if x == width { y += 1; x = 0 }
}
```

## 39. Accounting for iPhoneX notch
```swift
override func viewDidAppear(_ animated: Bool) {
     super.viewDidAppear(animated)
     view.frame = UIScreen.main.bounds.inset(by: view.safeAreaInsets)
}
override func viewDidLoad() {
     super.viewDidLoad()
     self.view = View(frame: .zero)
}
```

## 38. Store constant in array types
- Great way to store values in array types, or add custom methods to array types
- Allows you to get rid of clunky looking `Array where Element = SomeThing`
```swift
typealias ColorMapItem = UIColor
typealias ColorMap = [ColorMapItem]
extension ColorMap { // Array where Element == ColorMapItem
   static let rainbow = [.blue, .red, .yellow]
}
let rainbowColors: ColorMaps = .rainbow
```

## 37. Structure colors:
```swift
import UIKit
/**
 * ## Examples:
 * Color.Text.header // white
 * Color.UI.Background.secondary // lightGray
 */
struct Color {
   struct Text {
      static let header: UIColor = .white
      static let button: UIColor = .systemBlue
      static let description: UIColor = .lightGray
      static let paragraph: UIColor = .gray
      static let title: UIColor = .white
   }
   struct UI {
      struct Foreground {
         static let primary: UIColor = .darkGray
         static let secondary: UIColor = .gray
      }
      struct Background {
         static let primary: UIColor = .gray
         static let secondary: UIColor = .lightGray
      }
   }
}
```

## 36. Call the method in the alternating bool assert
- Imagine [r,g,b] are computational heavy methods
- The assert has to execute every method to assert
- By not pre-calling the methods before the group assert, you save cpu processing power
```swift
func r: Bool { print("r"); return true }
func g: Bool { print("g"); return false }
func b: Bool { print("b"); return true }
let valid: Bool = r && g && b
print(valid) // r,g false (skips calling b)
```

## 35. Using try with for loops
```swift
do {
   try (1...40).forEach { qrVersion in
      guard let qrVer = QRVer(rawValue: qrVersion - 1) else { throw NSError(domain: "unable to create qr-ver", code: 0) }
      guard let data: Data = QRStringData.randomData(config: (qrVer, .byte, .l)) else { throw NSError(domain: "unable to make data", code: 0) }
      guard let qrImage: Image = try? QRWriter.image(data: data, ecLevel: .l, moduleMultiplier: 16) else { throw NSError(domain: "unable to create UIImage", code: 0) }
      guard let ciImg: CIImage = qrImage.ciImage ?? qrImage.ciImage else { throw NSError(domain: "err ciimg", code: 0) }
      let symbolVersion: Int? = try? ciImg.symbolVersion()
      guard qrVersion == symbolVersion else { throw NSError(domain: "version: \(qrVersion) failed", code: 0) }
   }
   Swift.print("All symbol versions checked out")
   onComplete(true)
} catch {
   Swift.print("error:  \(error)")
   onComplete(false)
}
```


## 34. let variables in functions
The bellow is possible because index is guaranteed to be set and is much more readable than using a closure to do the same thing.
```swift
func doSomething() {
   let index: Int
   if versionNumber <= 9 { index = 0 }
   else if versionNumber <= 26 { index = 1 }
   else { index = 2 }
}
```


## 33. Queue up things with DispatchGroup:
You can nest closures, but nesting is often a "code-smell", using DispatchGroup and .wait can be a nice alternative.

```swift
typealias COMPLETION = () -> ()
func functionOne(completion: @escaping COMPLETION) {
    print("1")
    DispatchQueue.main.asyncAfter(deadline: .now() + 2.1) { completion() }
}
func functionTwo(completion: @escaping COMPLETION) {
    print("2")
    DispatchQueue.global(qos: .background).asyncAfter(deadline: .now() + 1) { completion() }
}
func functionThree(completion: @escaping COMPLETION) {
    print("3")
    DispatchQueue.main.asyncAfter(deadline: .now() + 0.6) { completion() }
}
DispatchQueue.global().async {
    let dispatchGroup = DispatchGroup()
    dispatchGroup.enter()
    functionOne { dispatchGroup.leave() }
    dispatchGroup.wait() //Add reasonable timeout
    dispatchGroup.enter()
    functionTwo { dispatchGroup.leave() }
    dispatchGroup.wait()
    dispatchGroup.enter()
    functionThree { dispatchGroup.leave() }
    dispatchGroup.wait()
    dispatchGroup.notify(queue: .main) {
        Swift.print("all done") // All tasks are completed
    }
} // this will print: 1, 2, 3, all done
```



## 32. "Switch" with additional guard clauses:
```swift
enum Flavours: String {
    case vanilla, chocolate, strawberry
}
func makeIceCream(flavour: Flavours, caneType: String? = nil) {
    if case .vanilla = flavour, let caneType = caneType {
        print("Vanilla with \(caneType)")
    } else if case .strawberry = flavour {
        print("Strawberry")
    } else if case .chocolate = flavour {
        print("Chocolate")
    } else {
        print("No 🍦 for you")
    }
}
makeIceCream(flavour: .vanilla, caneType: "Cane") // Vanilla with Cane
makeIceCream(flavour: .strawberry) // Strawberry
makeIceCream(flavour: .vanilla) // No 🍦 for you
```


## 31: Simulate network behaviour
Sleep for a random amount of time between 1 and 7 seconds. (Great for simulating async network calls etc)
```swift
sleep((1..<7).randomElement() ?? 1)
```


## 30. Recursive flatMap
```swift
/**
 * Multidimensional-flat-map...because flatMap only works on "2d arrays". This is for "3d array's"
 * - Note: A 3d array is an array structure that can have nested arrays within nested arrays infinite addendum
 * - Note: Alternate names for this method as suggest by @defrenz and @timvermeulen on slack swift-lang #random: `recursiveFlatten` or `recursiveJoined`
 * ## Examples:
 * let arr:[Any] = [[[1],[2,3]],[[4,5],[6]]] 👈 3d array (3 depths deep)
 * let x2:[Int] = arr.recursiveFlatmap()
 * Swift.print(x2)//[1,2,3,4,5,6]
 */
func recursiveFlatmap<T>() -> [T] {
    var results = [T]()
    for element in self {
        if let sublist = element as? [Self.Iterator.Element] { // Array
            results += sublist.recursiveFlatmap()
        } else if let element = element as? T { // Item
            results.append(element)
        }
    }
    return results
}
```

## 29. Access name of int enum:
This does not work on some native enums, often due to the fact that they are Objc enums, In such cases make an extension that has a switch that returns the name.
```swift
public enum TestEnum : Int {
   case one = 0, two, three
}
Swift.print("\(String(describing: TestEnum.three))") // three
// or another example:
public enum FocalType: Int, CaseIterable { case ultraWide, wide, tele }
print(FocalType.allCases.map { "\($0): \($0.rawValue)" }.joined(separator: ", "))
// ultraWide : 0, wide : 1, tele : 2
```

## 28. Long numbers:
```swift
//Bad
let valA: Int = 100000000 * 2
//Good:
let valB: Int = 100_000_000 * 2
```

## 27. Prefer contains over first
```swift
// Good
arr.first(where: { $0 == match }) != nil
// Better:
arr.contains(where: { $0 == match })
// Best
arr.contains { $0 == match }
```

## 26. Visual colors in xCode
A nice way to have visual representation of colors in code:

```swift
enum Colors {
	static let teal: UIColor = #colorLiteral(red: 0, green: 0.8039215686, blue: 0.8039215686, alpha: 1)
	static let lightTeal: UIColor = #colorLiteral(red: 0.6, green: 1, blue: 0.3921568627, alpha: 1)
	static let darkTeal: UIColor = #colorLiteral(red: 0, green: 0.6, blue: 0.6, alpha: 1)
}
// Colors.teal
```

## 25. Optional chaining
```swift
Swift.print(Optional("✅") ?? "🚫") // 🚫
Swift.print(Optional(nil) ?? "🚫") // ✅
```

It's like providing a default value if the optional is nil. You can do. it's equivalent to doing `Optional("") != nil ? Optional("") : ""`

## 24. Make rounded graphics look great
- Use `NSScreen.main.backingScaleFactor` for macOS and `UIScreen.main.scale` for iOS
- This ensures that rounded graphics looks sharp

```swift
self.caLayer?.rasterizationScale = 2.0 * Screen.mainScreenScale
self.caLayer?.shouldRasterize = true
```

## 23. Make methods off-limit
[https://www.mokacoding.com/blog/swift-unavailable-how-to/](https://www.mokacoding.com/blog/swift-unavailable-how-to/)

This is a great way to avoid having to repeat this method in every subclass that uses :UIView
```swift
@available(*, unavailable)
public required init?(coder: NSCoder) {
   fatalError("init?(coder:) is not supported")
}
```

## 22. Avoid xCode warning when returned value is not used:

```swift
@discardableResult
func add(a: Int, b: Int) -> Int {
    return a + b
}
```

## 21. Store reuse identifiers in a cell extension
There is also a small library that simplifies the registry and reuse syntax: [https://github.com/eonist/ReusableCell](https://github.com/eonist/ReusableCell)
```swift
class SomeTableViewCell: UITableViewCell{}
extension SomeTableViewCell {
   static let cellReuseIdentifier: String = "\(SomeTableViewCell.self)"
}
```

## 20. Flattening 3d array:
See also tip nr.40: Flattening nested loops:

```swift
struct Subscription { let type: String }
struct Account { let subscriptions: [Subscription] }
struct User { let accounts: [Account] }
let users: [User] = [
   User(accounts: [Account(subscriptions: [Subscription(type: "a")])]),
   User(accounts: [Account(subscriptions: [Subscription(type: "b")]),Account(subscriptions: [Subscription(type: "c")])]),
   User(accounts: [Account(subscriptions: [Subscription(type: "d"), Subscription(type: "e"), Subscription(type: "f")])])
]
// Not preferred:
let result1 = users.map { user in
   return user.accounts.map { account in
      return account.subscriptions.map { subscription in
         return subscription.type
      }
   }
}.flatMap{ $0 }.flatMap { $0 }
Swift.print("result1:  \(result1)")//["a", "b", "c", "d", "e", "f"]
// Preferred
let result2 = users.flatMap { $0.accounts }.flatMap { $0.subscriptions }.flatMap{ $0.type }
Swift.print("result2:  \(result2)")//["a", "b", "c", "d", "e", "f"]
```

### 19. Dot-syntax inference and array iteration with Enums

```swift
/**
 * TIPS: Access all colors via: Constants.Colors.allCases
 * ## Examples: Constants.Colors.allCases[1]//UIColor.yellow
 * ## Examples: Constants.Colors.red//UIColor.red
 * - Important: ⚠️️ the key must be unique and the the value must be unique
 */
class Constants{
   enum Colors: String, CaseIterable {
      case blue = "FB1B4D", yellow = "1DE3E6", red = "22FFA0", green = "FED845"
      var uiColor: UIColor {
         return UIColor.init(hex: self.rawValue)
      }
   }
}
```

### 18. Combinational types instead of generics

The `setCardConstraints` method requires conformance to UIView and ConstraintKind

```swift
// Generics
func setCardConstraints<T: UIView>(card: T) where T: ConstraintKind { // 👈 Looks messy
   card.applyConstraint{ view in
      // do stuff
   }
}
// Combination type:
public typealias UIViewConstraintKind = UIView & ConstraintKind
func setCardConstraints(card: UIViewConstraintKind) { // 👈 Looks much cleaner
   card.applyConstraint{ view in
      // do stuff
   }
}
```

### 17. Delay something
```swift
DispatchQueue.main.asyncAfter(deadline: .now() + 4) {
   // Do something after 4 seconds have passed
}

// Alternatively:
DispatchQueue.global(qos: .background).async { // asyncAfter also works here, instead of sleep
    sleep(4)
    print("Active after 4 sec, and doesn't block main")
    DispatchQueue.main.async {
        // Do stuff in the main thread here
    }
}

// Alternatively:
let second: Double = 1000000
usleep(useconds_t(0.002 * second)) // will sleep for 2 milliseconds (.002 seconds)

// Alternatively:
/**
 * Supports fractional time
 * ## Examples:
 * sleep(sec: 2.2) // sleeps for 2.2 seconds
 */
public func sleep(sec: Double){
    usleep(useconds_t(sec * 1000000)) // wait for n secs
}

// Pausing in a loop:

let array = [1, 2, 3]
DispatchQueue.global(qos: .background).async {
    array.forEach { i in
        sleep(1)
        DispatchQueue.main.async {
            Swift.print("i: \(i)") // increments every second and prints: 1,2,3
        }
    }
}
```

### 16. Enums in a closure to describe events
```swift
class MyViewController {
    enum Error: Swift.Error {
        case invalidUsername
        case invalidPassword
    }
    enum Event {
        case loginSuccess
        case showErrorMessage(Error) // - showErrorMessage: We errored, we need to show an error message
    }
    typealias EventAction = (Event) -> Void
    var eventHandler: EventAction?
    func login(name: String, password: String) {
         guard "test" == user.password else {
            eventHandler(.showErrorMessage(.invalidUsername))
            return
         }
         guard "Monica" == user.name else {
            eventHandler(.showErrorMessage(.invalidUsername))
            return
         }
         eventHandler(.loginSuccess)
    }
}
func handler(event: Event) {
   switch event {
   case .loginSuccess:
      print("user logged in")
   case .showErrorMessage(let error):
      print("user did not log in, reason: \(error.description)")
   }
}
let controller = MyViewController()
controller.eventHandler = handler
controller.login(name: "John", password: "abc123")
```

### 15. Rethrows:
The `rethrows` keyword indicates to the compiler that the outer function is a throwing function only if the closure passed in throws an error that is propagated to the current scope. Basically with `rethrows`, we can use throw inside the closure. When the error handlers are called within the function we use throws.

```swift
typealias MagicalOperation = () throws -> MagicalResult
func doSomethingMagical(magicalOperation: MagicalOperation) rethrows -> MagicalResult {
  return try magicalOperation()
}
```

### 14 Use custom closures with the native sortedBy method:
```swift
enum CardType {
   case heart, spades, diamond, clover
}
enum CardValue{
  case two, three, four, five, six, seven, eight, nine, ten, prince, queen, king, ace
}
typealias Card = (type: CardType, value: CardValue)
typealias Sorter = ((_ element1: Card, _ element2: Card) -> Bool)
//  arrayTest
func arrayTest(){
  let someCards: [Card] = [(.spades, .eight), (.heart, .seven), (.heart, .king), (.diamond, .ace), (.heart, .two)]
  let findHearts: [Card] = someCards.filter { return $0.type == .heart }
  Swift.print("unsorted.🔵")
  findHearts.forEach { (card: Card) in
      Swift.print("card.type:  \(card.type) card.value:  \(card.value)")
  }
  Swift.print("unsorted.🔴")
  let sorter: Sorter = { (element1, element2) -> Bool in
      return element1.value.hashValue < element2.value.hashValue
  }
  let sortedHearts = findHearts.sorted { sorter($0, $1) } // sort the cards
  Swift.print("sorted.🔵")
  sortedHearts.forEach { Swift.print("$0.type:  \($0.type) $0.value:  \($0.value)") } // print the cards
  Swift.print("sorted.🔴")
}
```

### 13 Manipulate an object in a closure

```swift
@discardableResult // 👈 Avoids XCode compiler warnings if result is not used
func with<T>(_ item: T, update: (inout T) throws -> Void) rethrows -> T {
    var item = item
    try update(&item)
    return item
}
//EXAMPLE 1:
let rectangle: CGRect = with(.init(x: 0, y: 0, width: 100, height: 100)) {
  $0 = $0.offsetBy(dx: 20, dy: 20)
  $0 = $0.insetBy(dx: 10, dy: 10)
}
Swift.print(rectangle) // X:30.0, y:30.0, width:80.0, height:80.0
//EXAMPLE 2:
let color = with(UIColor.init(red: 50, green: 100, blue: 0, alpha: 0.9)) { ( col:inout UIColor) -> Void in
  col = col.withAlphaComponent(0.2)
}
Swift.print(color.cgColor.alpha) // 0.2
```

[https://github.com/eonist/with](https://github.com/eonist/with)



### 12. Result (for async callback returns)

⚠️️ improve this: ⚠️️

```swift
enum Result<Value> {
    case success(Value)
    case failure(Swift.Error)
}

func start(_ completionHandler: @escaping (Result<Any>) -> Void) -> FBSDKGraphRequestConnection{
    return start() { (_, response, error) in
        switch (response, error) {
        case (.some(let result), .none):
            completionHandler(Result(value: result))

        case (.none, .some(let error)):
            completionHandler(Result(error: error))

        case (.none, .none), (.some, .some):
            preconditionFailure("Unexpected State")
        }
    }
}

switch result {
case .success (let value as [ String : Any ]) where value["email"] is String:
    let email = value["email"] as! String
    print("email: \(email)")
default:
    self.present(error: Error(.facebookOther))
}
```
https://stackoverflow.com/questions/51235876/swift-pattern-matching-switch-downcasting-and-optional-binding-in-a-single-s



### 11. Code injection via Protocol extension

```swift
protocol FruitKind {
   func eat() // Class must implement doSomething
}
extension FruitKind {
   func eat() { print("Tastes like fruit") } // default implementation
}
protocol AppleKind: FruitKind {} // inherit base protocol
extension AppleKind {
   func eat() { print("Tastes like 🍎") }// override default implementation
}
protocol PearKind: FruitKind {} // inherit base protocol
extension PearKind {
   func eat() { print("Tastes like 🍐") }// override default implementation
}
class Fruit: FruitKind {} // Make a class
extension Fruit: AppleKind {} // 👈 you sort of attach custom apple functionality
let fruit = Fruit() // create an instance of Class
fruit.eat() //  Tastes like 🍎 (calls the AppleKind.eat instead of Fruit.eat and prints)
class AnotherFruit: FruitKind {}
extension AnotherFruit: PearKind {}  // 👈 you sort of attach custom pear functionality
let anotherFruit = AnotherFruit()
anotherFruit.eat() // Tastes like 🍐 (calls the AppleKind.eat instead of Fruit.eat and prints)
```


### 10. Nifty array trick:

```swift
let result:[String] = Array(repeating: "🎉", count: 3)
print(result) // 🎉🎉🎉
```

```swift
/**
 * ## Examples: Margin.bottom.rawValue//32
 * Margin.allCases[1].rawValue//32
 * - Note: values must be unique
 */
enum Margin: CGFloat, CaseIterable{
   case top = 24, bottom = 32, horizontal = 12
}

```



### 9. Closure Generics

```swift
typealias UIViewConstraintKind = UIView & ConstraintKind // This works almost like someValue:T .... where T:ConstraintKind
typealias ReturnType = (anchor: AnchorConstraint, size: SizeConstraint) // This just makes the method that returns this simpler
typealias ConstraintKindClosure = (_ view: UIViewConstraintKind) -> ReturnType
/**
 * NOTE: We use the "combination-type": `UIViewConstraintKind` since closures can't do regular generics like t:UIView where Self:ConstraintKind
 */
func activateConstraintKind(closure: ConstraintKindClosure) {
   let constraints:ReturnType = closure(self) // the constraints is returned from the closure
   //...do something with the constraints
}
```

### 8. Accessing raw and hashValue of enum

```swift
enum CellType: String {
    case primary, secondary, tierary
}
let possibleCellType = CellType(rawValue: "tierary")
possibleCellType // tierary
possibleCellType?.hashValue // 2
```

### 7. String enum's
No need to hard code the string, as long as the enum type is string 👌, the name is auto converted to string when you call rawValue
```swift
enum CellType: String {
   case primary, secondary, tierary
}
print("\(CellType.primary.rawValue)") // primary
print("\(CellType.tierary.rawValue)") // tierary
```



### 6. Override static variable

```swift
class var id : String { return "\(HorCell.self)" } // In a class
override class var id : String { return "\(PrimaryCell.self)" } // In a sub-class of the class
```


### 5. Action as argument
In the case bellow we use an argument to assign the target. It could be possible to pass a ref to the buttonTouched method as well. Example of that comming soon.

```swift
func createBtn(action: String) -> UIButton {
    let btn: UIButton = .init(type: .system)
    btn.addTarget(self, action: Selector(action), for: .touchUpInside)
    return btn
}
@objc func buttonTouched(_ sender: UIButton) {
    Swift.print("buttonTouched")
}
let btn = createButton(action: "buttonTouched:") // 👈 The : character is important
```



### 4. Simplify similar code with a closure

```swift
let closure = { (text: String, bgColor: UIColor, y: CGFloat, action: String) in
   let btn: UIButton = .init(type: .system)
   btn.backgroundColor = bgColor
   btn.setTitle(text, for: .normal)
   btn.titleLabel?.font =  .systemFont(ofSize: 12)
   btn.frame = .init(x:00, y:y, width:100, height:50)
   btn.addTarget(self, action: Selector(action), for: .touchUpInside)
   self.addSubview(btn)
}
// btn1
closure(
   "Forward",
   .gray,
   250,
   "onForwardButtonClick"
)
// btn2
closure(
   "Back",
   .lightGray,
   250,
   "onbackButtonClick"
)
```

### 3. Asserting if an array index exist:

```swift
let arr = [1, 2, 3]
if let fourthItem = (3 < arr.count ?  arr[3] : nil ) {
     Swift.print("fourthItem:  \(fourthItem)")
}else if let thirdItem = (2 < arr.count ?  arr[2] : nil) {
     Swift.print("thirdItem:  \(thirdItem)")
}
// Output: thirdItem: 3
```


### 2. Simple Caching:
```swift
/**
 * Also works for external server data
 * ## Examples: if let data = myBigData { print(data) }
 */
private var _myBigData : Data? = nil
var myBigData : Data? {
    set {
        self._myBigData = newValue
    } get {
        if _myBigData == nil {
            // ... get a reference to file on disk, f ...
            if let d = try? Data(contentsOf:f) {
                self._myBigData = d
                // ... erase the file ...
            }
        }
        return self._myBigData
    }
}
```

### 1. Sometimes using switch can be overkill:
```swift
enum State {
    case a, b, c
}
let state: State = .b
if [.a, .b].contains(state) {
    Swift.print("either a or b")
}else if case .a = state {
    Swift.print("must be c")
}
```
