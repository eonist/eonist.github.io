Some of my favourite swift tricks<!--more-->

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

### 3. Asserting if an array index exist:

```swift
let arr = [1,2,3]
if let fourthItem = (3 < arr.count ?  arr[3] : nil ) {
     Swift.print("fourthItem:  \(fourthItem)")
}else if let thirdItem = (2 < arr.count ?  arr[2] : nil) {
     Swift.print("thirdItem:  \(thirdItem)")
}
// Output: thirdItem: 3
```

### 4. Simplify similar code with Closure

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
let btn = createButton(action: "buttonTouched:") // 👈The : character is impportant
```


### 6. Override static variable

```swift
class var id : String { return "\(HorCell.self)" } // In a class
override class var id : String { return "\(PrimaryCell.self)" } // In a sub-class of the class
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


### 8. Accessing raw and hash of enum

```swift
enum CellType: String {
    case primary, secondary, tierary
}
let possibleCellType = CellType(rawValue: "tierary")
possibleCellType // tierary
possibleCellType?.hashValue // 2
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

### 10. Nifty array trick:

```swift
let result:[String] = Array(repeating: "🎉", count: 3)
print(result) // 🎉🎉🎉
```


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


### 15. Rethrows:

The `rethrows` keyword indicates to the compiler that the outer function is a throwing function only if the closure passed in throws an error that is propagated to the current scope. Basically with `rethrows`, we can use throw inside the closure. When the error handlers are called within the function we use throws.

```swift
typealias MagicalOperation = () throws -> MagicalResult
func doSomethingMagical(magicalOperation: MagicalOperation) rethrows -> MagicalResult {
  return try magicalOperation()
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

let array = [1,2,3]
DispatchQueue.global(qos: .background).async {
    array.forEach { i in
        sleep(1)
        DispatchQueue.main.async {
            Swift.print("i: \(i)") // increments every second and prints: 1,2,3
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
// Combinational type
public typealias UIViewConstraintKind = UIView & ConstraintKind
func setCardConstraints(card: UIViewConstraintKind) { // 👈 Looks much cleaner
   card.applyConstraint{ view in
      // do stuff
   }
}
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


## 20. Flattening 3d array:

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

## 21. Store reuse identifiers in a cell extension
There is also a small library that simplifies the registry and reuse syntax: [https://github.com/eonist/ReusableCell](https://github.com/eonist/ReusableCell)
```swift
class SomeTableViewCell: UITableViewCell{}
extension SomeTableViewCell {
   static let cellReuseIdentifier: String = "\(SomeTableViewCell.self)"
}
```


## 22. Avoid XCode warning when returned value is not used:

```swift
@discardableResult
func add(a: Int, b: Int) -> Int {
    return a + b
}
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

## 24. Make rounded graphics look great
- Use `NSScreen.main.backingScaleFactor` for macOS and `UIScreen.main.scale` for iOS
- This ensures that rounded graphics looks sharp

```swift
self.caLayer?.rasterizationScale = 2.0 * Screen.mainScreenScale
self.caLayer?.shouldRasterize = true
```

## 25. Optional chaining
```swift
Swift.print(Optional("✅") ?? "🚫") // 🚫
Swift.print(Optional(nil) ?? "🚫") // ✅
```

It's like providing a default value if the optional is nil. you can do. it's equivalent to doing `Optional("") != nil ? Optional("") : ""`

## 26. Visual colors in xcode

A nice way to have visual representation of colors in code:

```swift
enum Colors {
	static let teal: UIColor = #colorLiteral(red: 0, green: 0.8039215686, blue: 0.8039215686, alpha: 1)
	static let lightTeal: UIColor = #colorLiteral(red: 0.6, green: 1, blue: 0.3921568627, alpha: 1)
	static let darkTeal: UIColor = #colorLiteral(red: 0, green: 0.6, blue: 0.6, alpha: 1)
}
// Colors.teal
```

## 27. Prefer contains over first
```swift
//Good
arr.first(where: { $0 == match }) != nil
//Better:
arr.contains(where: { $0 == match })
//Best
arr.contains { $0 == match }
```

## 28. Long numbers:
```swift
//Bad
let valA: Int = 100000000 * 2
//Good:
let valB: Int = 100_000_000 * 2
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

## 31: Simulate network behaviour
Sleep for a random amount of time between 1 and 7 seconds. (Great for simulating async network calls etc)
```swift
sleep((1..<7).randomElement()!)
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

## 38. Store constant in array types
- Great way to store values in array types, or add custom methods to array types
- Allows you to get rid of clunky looking `Array where Element = SomeThing`
```swift
typealias ColorMapItem = UIColor
typealias ColorMap = [ColorMapItem]
extension ColorMap { // Array where Element = ColorMapItem
   static let rainbow = [.blue, .red, .yellow]
}
let rainbowColors: ColorMaps = .rainbow


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
### 41. Use an online swift playground for quick tests:

[http://online.swiftplayground.run](http://online.swiftplayground.run)

```swift
import Foundation

print("Hello World")

print((1...40).contains(1)) // true
print((1...40).contains(40)) // true

```


### 42. Map ranges:

```swift
func randInt() -> Int { return Int(arc4random()) }

let randomArray = (1...4).map { _ in randInt() } // 3,1,2,2
```

### 43. Do many things simultaneously and call onComplete when things are done
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

### 45: Mark methods as deprecated and get warning :
- Document API changes with @available keyword.
```swift
@available(*, deprecated, renamed: "newMethodName")
func foo() {
  ...
}
// Each time foo() is called, a deprecation warning will appear:
// foo is deprecated, renamed `newMethodName`
```
