Some of my favourite swift tricks<!--more-->


### 220. Run multiple swift test locally from one call:

To call `swift test` from multiple paths in the terminal, you can create a bash script that iterates through the desired directories and runs the command in each one. Here's an example of how you can achieve this:

```bash
#!/bin/bash

# Array of paths to test
paths=(
    # Make sure these are absolute paths. for relatve paths remember to step out of the path. Ask copilot for what to edit regarding that
    "path/to/project1/"
    "path/to/project2/"
    "path/to/project3/"
)

# Loop through each path and run swift test
for path in "${paths[@]}"
do
    echo "Running tests in $path"
    cd "$path"
    swift test
    # You can also stop the loop if a test fails. Ask copilot what to add regarding that
    echo "Finished testing $path"
    echo "------------------------"
done
```

This script does the following:

1. Defines an array of paths where you want to run `swift test`.
2. Loops through each path in the array.
3. Changes the current directory to the specified path.
4. Runs `swift test` in that directory.
5. Prints a message when finished with each path.

To use this script:

1. Save it with a `.sh` extension (e.g., `run_swift_tests.sh`).
2. Make it executable by running `chmod +x run_swift_tests.sh` in the terminal.
3. Run the script using `./run_swift_tests.sh`.

This approach allows you to easily add or remove paths as needed, and it will run `swift test` in each specified directory.


### 219. Simple darkmode colors:
```swift
 // Example of using dynamic colors
let backgroundColor = Color(UIColor { traitCollection in
    return traitCollection.userInterfaceStyle == .dark ? .black : .white
})
```

### 218. enum with new cases in the future:

```swift
public enum MyEnum {
    case first
    case second
}
// The @unknown default case ensures that your code will still compile if new cases are added in the future8.

switch myEnum {
    case .first:
        // Handle first case
    case .second:
        // Handle second case
    @unknown default:
        // Handle any future cases
}

// If you want to create an enum that cannot be extended in the future, you can use the @frozen attribute:

@frozen public enum MyFrozenEnum {
    case first
    case second
}
```

### 217. Calling async methods, w/o making the calling method async
To call an async method without making the calling method async in Swift, you can use a `Task`. This allows you to execute asynchronous code from a synchronous context. Here's how you can do it:

```swift
func synchronousMethod() {
    Task {
        do {
            let result = try await asyncMethod()
            // Handle the result
        } catch {
            // Handle any errors
        }
    }
}

func asyncMethod() async throws -> SomeType {
    // Async implementation
}
```

This approach creates a new task that runs concurrently, allowing the synchronous method to continue execution without waiting for the async method to complete[1][5]. However, there are some important considerations:

1. The task will run independently, so the synchronous method won't wait for its completion.
2. Any exceptions thrown in the async method won't be propagated to the calling method unless explicitly handled within the task 
3. If you need the result immediately, this approach won't work as the task runs asynchronously 

For cases where you need to wait for the result in a synchronous context, you might consider using a semaphore or other synchronization primitives, but this is generally discouraged as it can lead to deadlocks and poor performance 

It's important to note that while this method allows calling async functions from synchronous code, it's generally better to embrace asynchronous programming throughout your codebase when possible, using "async/await all the way down" 
  

### 216. Make packages run on a random time everyday in github action

1. Select the github/workflows/*.yml file
2. Use cursor + o1 and prompt: "make this build and test everyday at a random time. But also make it build and test for pullrequest and push, use bash to accomplish it"

Code similar to this will be added:
```yml
  schedule:
    - cron: '0 0 * * *' # Runs daily at midnight UTC

- name: Wait for Random Time
      if: github.event_name == 'schedule'
      shell: bash
      run: |
        RANDOM_DELAY=$(( RANDOM % 86400 ))
        echo "Waiting for $RANDOM_DELAY seconds"
        sleep $RANDOM_DELAY
```

### 215. Switch if in github action environment:
- This line checks whether the GITHUB_ACTIONS environment variable exists.
- If the variable exists (i.e., the code is running in GitHub Actions), the 
```swift
if ProcessInfo.processInfo.environment["GITHUB_ACTIONS"] != nil {
    print("GA")
} else { 
    print("not GA")
}
```

### 214: Flat switching on nested enums

```swift
enum OuterEnum {
    enum FormatType {
        case someType
        case otherType
    }
    
    case format(FormatType)
    case otherCase
}
let value: OuterEnum = .format(.someType)
switch value {
    case .format(let type) where type == .someType:
        print("Matched format with someType")
    case .format:
        print("Matched format with other type")
    case .otherCase:
        print("Matched other case")
}
```

### 213. allSatisfy
Great for testing. Here is a quick example:
```swift
func testRandomString() throws {
    let randomWord: String = String((0..<Int.random(in: 2...20)).map { _ in Character(UnicodeScalar(Int.random(in: 97...122))!) })
    Swift.print("randomWord:  \(randomWord)")
    let words: [String] = [
        randomWord,
        "abracadabra",
        "onboarding",
        "appActiv",
        "af_active",
        "auth"
    ]
    let allSatisfy = words.allSatisfy { word in
        word.consistentRandom10Digits.count == 10 &&
        word.consistentRandom10DigitNumber.count == 10 &&
        word.consistentRandom10Digits == word.consistentRandom10Digits &&
        word.consistentRandom10DigitNumber == word.consistentRandom10DigitNumber
    }
    print("allSatisfy: \(allSatisfy ? "✅" : "❌")")
    XCTAssertTrue(allSatisfy)
}
```

### 212. Combinding fencing clauses:
```swift
#if DEBUG && os(macOS)
// DO things that only apply in debug and for macOS
#endif
```

### 211. Adding xcodeproj to a SPM package:
1. Add a new xcodeproj to the root of the package
2. Add local package in the xcodeproj and pick the root of the package
3. Now you can import the package in your xcodeproj code

### 210. Extending optional types 
```swift
extension Optional where Wrapped == String {
    var isNilOrEmpty: Bool {
        return self?.isEmpty ?? true
    }
}
```

### 209. Password generator - Random string

randomString(length: 6) // dff32KS

```swift
func randomString(length: Int) -> String {
      // Define the characters that can be used in the random string
      let characters: String = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
      // Generate an array of random characters from the `characters` string
      let randomCharacters: [String.Element] = (0..<length).compactMap { _ in
         characters.randomElement()
      }
      // Convert the array of characters into a string and return it
      return String(randomCharacters)
   }
```

### 208. if / else statements as expressions

This makes the code easier to read and write, especially when you need to assign a value to a variable based on multiple conditions.

Before, you might have used nested ternary operators, which can be hard to read:

```swift
// Assign a value to 'output' based on multiple conditions
let output = (condA ? valA :
              condB ? valB :
              condC ? valC :
              valD)
```

With Swift 5.9, you can use if/else expressions instead, which are easier to understand:

```swift
// Assign a value to 'output' using if/else expressions
let output = if condA {
    valA
} else if condB {
    valB
} else if condC {
    valC
} else {
    valD
}
```

### 207. switch statements as expressions
This new feature is also useful when you need to assign a value to a global variable or a stored property based on a switch statement. Before, you would have to use a closure:

```swift
// Assign a value to 'sound' based on the type of 'creature'
let sound: String = {
    switch creature {
    case .dog:
        return "Woof"
    case .cat:
        return "Meow"
    case .cow:
        return "Moo"
    default:
        return "Unknown sound"
    }
}()
```

Now, you can use a switch expression, which is simpler and cleaner:

```swift
// Assign a value to 'sound' using a switch expression
let sound: String = switch creature {
    case .dog:
        "Woof"
    case .cat:
        "Meow"
    case .cow:
        "Moo"
    default:
        "Unknown sound"
}
```

### 206. Native with:

```swift
with is awesome, but you have to add it to every app and lib to use it. Instead here is a native way to get with like capabilities:
[page].forEach {
    $0.name = "John"
    $0.page = 3
    $0.publishedAt = .now
}
```

### 205. Simple performance timing:
```swift
let c: CFTimeInterval = CACurrentMediaTime()
usleep(useconds_t(0.002)) // Simulates heavy task here for 2 milliseconds (.002 seconds)
let c1: CFTimeInterval = CACurrentMediaTime() - c
Swift.print("Time past: \(c1)") // 0.002
```

### 204. Dynamic lookup on disctionary:
The documentation at https://docs.swift.org/swift-book/ReferenceManual/Attributes.html says that you can use this feature to find members of a class, structure, enumeration, or protocol by their name when the program is running. To use this, the type needs to have a special function called `subscript(dynamicMemberLookup:)`.
```swift
@dynamicMemberLookup
struct Employee {
    var data = [String: String]()
    subscript(dynamicMember member: String) -> String {
        return data[member] ?? "Data not available"
    }
}
var employee = Employee()
employee.data["name"] = "Jane"
print(employee.name) // Jane
print(employee.position) // Data not available
```

Even though the Contact class doesn't have a 'name' or 'email', the program won't give an error. This is because it checks for these members when it's running, not before. If it can find 'name' or 'email', it uses those. If it can't, it just uses a default value.

### 203. Convert OrderedDictionary to Dictionary:
```swift
import OrderedCollections // get this from apples SPM repo on github

let x: OrderedDictionary = [
  "200": "OK",
  "403": "Access forbidden",
  "404": "File not found",
  "500": "Internal server error",
]

extension OrderedDictionary where Key: Hashable, Value: Any {
   var dictionary: [Key: Value] {
      self.reduce(into: [Key: Value]()) { (result, element) in
         result[element.key] = element.value
      }
   }
}
x.dictionary // outputs regular dictionary
```

### 202. Get a dictoary from a class or struct instance: 
```swift
func asDictionary(target: Any) -> [String: Any] {
   let mirror = Mirror(reflecting: target)
   let dict: [String: Any] = Dictionary(uniqueKeysWithValues: mirror.children.lazy.map({ (label: String?, value: Any) -> (String, Any)? in
      guard let label = label else { return nil }
      return (label, value)
   }).compactMap { $0 })
   return dict
}
```
usage: 
```swift
let p1 = Person(name: "Ryan", position: 2, good : true, car:"Ford")
print(p1.asDictionary) // outputs dictionary
```
### 201. Generic typealias:
Generic typealias can be used to simplify param types etc

```swift
typealias Parser<A> = (String) -> [(A, String)]
```
Usage: 
```swift
func parse<A>(stringToParse: String, parser: Parser) 
```

### 200. AnyComparable

In Swift 5.9, you can write a method using parameter packs. Here I implemented the sorting using the built-in KeyPathComparator:

```swift

extension Array {
    mutating func sort<each T: Comparable>(by keyPaths: repeat (KeyPath<Element, each T>, SortOrder)) {
        var comparators: [KeyPathComparator<Element>] = []
        func addComparator<Key: Comparable>(_ keyPathOrder: (KeyPath<Element, Key>, SortOrder)) {
            comparators.append(KeyPathComparator(keyPathOrder.0, order: keyPathOrder.1))
        }
        // here I am technically creating a tuple, with its elements all being addComparator calls
        // this could be written more readably when we can iterate over a parameter pack with a for loop
        (repeat addComparator(each keyPaths))
        sort(using: comparators)
    }
}
```

Example Usage:

```swift
// order by length of string ascendingly, then by the string itself descendingly
someStrings.sort(by: (\.count, .forward), (\.self, .reverse))

```
 With that, you can write your sort method signature as:

```swift
mutating func sort(by criteria: (path: KeyPath<Element, AnyComparable>, order:OrderType)...) {
    ...
}
```

To make it easier for us to pass key paths with the type AnyComparable in, we can make an extension:

```swift
extension Comparable {
    // this name might be too long, but I'm sure you can come up with a better name
    var anyComparable: AnyComparable {
        .init(self)
    }
}
```

Now we can do:
```swift

someArray.sort(by: (\.key1.anyComparable, .asc), (\.key2.anyComparable, .asc))
```

### 199. Extension for Array where Element is Optional  
This might be a bit complex, but it's possible to create an AnyOptional protocol. This protocol would require an associated type (Wrapped) and a computed property to return the optional type. If the index is valid, the element is returned unwrapped; if not, nil is returned.

```swift
protocol AnyOptional {
    associatedtype Wrapped
    var optional: Optional<Wrapped> { get }
}

extension Optional: AnyOptional {
    var optional: Optional<Wrapped> { self }
}
extension Collection {
    subscript(safe index: Index) -> Element? {
        indices.contains(index) ? self[index] : nil
    }
}
extension Collection  {
    subscript(safe index: Index) -> Element.Wrapped? where Element: AnyOptional {
        indices.contains(index) ? self[index].optional ?? nil : nil
    }
}
var myArray: [String?] = ["2", "banana", nil, "31"]
var myStringArray: [String] = ["2", "3"]

let item = myArray[safe: 1] // item is String?
let strItem = myStringArray[safe: 99] // strItem is String?
```

### 198. How to use optional binding in swit
This involves "rebinding" (⚠️️ There might be a weay to make this generic ⚠️️)
```swift
import SwiftUI

extension Binding where Value == Date? {
    func flatten(defaultValue: Date) -> Binding<Date> {
        Binding<Date>( // We "rebin"d" here
            get: { wrappedValue ?? defaultValue },
            set: {  wrappedValue = $0 }
        )
    }
}

DatePicker(
    "",
    selection: $date.flatten(defaultValue: Date()), // Use the optional value, if it's nil use the default
    displayedComponents: .hourAndMinute
)
.datePickerStyle(WheelDatePickerStyle())
```

### 197. Singleton variable for SwiftUI view
SwiftUI views cant be singletons. But we can tie a global variable to a view. This example shows how we can control showing a sheet from anywhere. If the view is available.
```swift
var prefrencesSheetHandler = PrefsSheetHandler() // must be in global scope
/**
 * - Note: Solution found here: https://stackoverflow.com/questions/70870750/swiftui-modify-state-var-from-remote-closure
 * ## Examples:
 * prefsSheetHandler.isShowingSheet = true // <-- add anywhere
 * $prefsSheetHandler.isShowingSheet // add to sheet isPresneting variable
 * @ObservedObject var prefsSheetHandler: PrefsSheetHandler = prefrencesSheetHandler // add in the view scope
 */
class PrefsSheetHandler: ObservableObject {
   @Published var isShowingSheet = false
}
```

### 196. Multi cursor in xCode:
- Draw multiple lines: alt + drag
- Select multiple cursor positions: ctrl + cmd

### 195. Check if a string is an email

```swift
// "kenmueller0@gmail.com".isValidEmail // true
extension String {
    var isValidEmail: Bool {
        NSPredicate(format: "SELF MATCHES %@", "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}").evaluate(with: self)
    }
}
```
### 194. Finding Elements of Specific Type in Swift

```swift
extension Array {
    func whereType<T> () -> [T] {
        compactMap { $0 as? T } // The function "compactMap(" in Swift is incredibly useful. It maps each element of an array to another optional type and returns the value if it exists (is not null).
    }
}
func testIt() {
    let array = [1, 2, "3", 4.0, 5] as [Any]
    let ints: [Int] = array.whereType? // You now have the ability to filter elements in an array based on their specific types.
    print(ints) // prints [1, 2, 5]
}
```

### 193. Sum of Any Numeric Types in Swift
In Swift, all numeric values, including "Int", "Float", "Double", and "CGFloat", conform to the "Numeric" protocol. By applying a generic constraint to your array extension, you can make methods available for all numeric types in your array.
```swift
extension Array where Element: Numeric){
    sum () -> Self. Element {
        reduce (0, +) //By employing the "reduce" function and the "+" operator, you can efficiently compute the sum of all elements in the array. Isn't that neat?
    }
}
// Observe how the "sum" function's return value matches the type of the array's elements!
func testit( {
    let sumOfDoubles = [1.1, 4,4, 8.8]. sum ()
    print (sum0fDoubles) // 14.3
    let sumOfInts = [1, 4, 8]. sum() *
    print(sumOfInts) // 13.0
    let sumOfFloats: Float = [1.1, 4,4, 8.81. sum ()
    print (sumOfFloats) // 14.3
}
```

### 192. Async getters in Swift 5.5+

```swift
struct LazyImageFromFile {
    enum Errors: Error {
     case invalidFilePath
    }
    let filePathOnDisk: URL
    init(filePathOnDisk: URL) throws { // In the constructor of our struct, we accept a file URL. If the URL does not point to a valid file, an error will be thrown.
        guard filePathOnDisk.isFileURL else {
        throw Errors.invalidFilePath
        self.filePathOnDisk = filePathOnDisk
     }
}
var data: Data {
    get async throws { // Introduced in Swift 5.5, you can now establish a getter as an asynchronous getter. This implies that the receiver will need to use "await", similar to JavaScript or Flutter, to obtain its underlying value.
        try Data( contents0f: self. filePathOnDisk,
        options: .mappedIfSafe)
    }
}
func testIt () async throws { // In the function where we are testing our structure, we designate it as an asynchronous function. This allows us to use the "await" keyword when calling our data's asynchronous getter.
    guard let fileUrl = Bundle.main.url( forResource: "myimage"
        withExtension: "jpg") else {
            return
    }
    let lazyImage = try LazyImageFromFile(filePathOnDisk: fileUrl)
    let contents = try await lazyImage.data // Finally, we will access the getter from our struct using the "try await" syntax, as the getter is marked as asynchronous and can potentially throw an error.
    print (contents)
}
```

### 191. Actors in Swift 5.5+:

```swift
actor Stack<T> { // The keyword "actor" introduced in Swift 5.5+ facilitates the creation of isolates. These isolates, managed by the actor, ensure thread-safety at a fundamental level within the actor by maintaining it's isolated data types.
    private var _items = [T] ()
    init items: [T] = [1) {
    self._items = items
    @discardableResult
    func push (_ value: T) async -> T {
        await _items.append(value) // Despite the "append(" function of Array in Swift not being an asynchronous function, by prefixing it with 'await' within this asynchronous function, we indicate that we are establishing a lock on our "_items" private variable.
        return value
    }
    func pop () async -> T {
        let popped = await _items.removeLast() // Likewise, when we are deleting an item from the end of our stack, we use 'await' on the result. Even though 'removeLast(' is not an asynchronous function, this approach establishes a lock on '_items' until 'removeLast' has completed its operation.
        return popped
    }
    var items: [T] { 
        get async { //Additionally, we provide a read-only access to our items using an asynchronous getter, a feature introduced in Swift 5.5+.
            _items
        }
    }
}
func testIt() async {
    let stack1 = Stack<Int> ( [1, 2, 31)
    let stack2 = Stack<Int> ( [4, 5, 6])
    await stack2.push (await stack1.push(10))
    await stack2.push(await stack1.pop())
    let stack1Count = await stack1.items. count
    let stack2Count = await stack2.items. count
    assert (stack1Count == 3)
    assert (stack2Count == 5)
}
```

### 190. Lazy Local Variables in Swift 5.5+
Beginning with Swift 5.5, it's possible to declare lazy local variables that are only evaluated upon their first use, similar to how lazy variables function at the class or struct level.

```swift
func incremented (_ value: Int) -> Int {
    value + 1 
}
func testIt() {
    var value = 1
    lazy var inc = incremented (value)
    value += 1
    print (inc) // 3
}
// Here, we initialize 'value' to 1. The 'incremented' function, which takes 'value' as an argument, won't be invoked until 'value +=1' is executed. This is because 'value' is first used in the print statement.
```

### 189. Accessing Plist directly

```swift
if let path = NSBundle.mainBundle().pathForResource("Config", ofType: "plist") {
  if let dict = NSDictionary(contentsOfFile: path) as? Dictionary<String, AnyObject> {
    // use swift dictionary as normal
  }
}
```

### 188. Different ways to create random numbers

See also: https://nemecek.be/blog/89/randomness-in-swift-comprehensive-overview

```swift
let randomBool = Bool.random()
let randomInt = Int.random(in: 1...6) // dice roll
let randomFloat = Float.random(in: 0...1)
let randomDouble = Double.random(in: 1..<100)
```

### 187. Useful string modifiers and parsers:
```swift
// Beginning of a string
let index = str.index(str.startIndex, offsetBy: 5)
let mySubstring = str[..<index] // Hello

// Prefix:
let index = str.index(str.startIndex, offsetBy: 5)
let mySubstring = str.prefix(upTo: index) // Hello
let mySubstring = str.prefix(5) // Hello

// End of a string subscripts:
let index = str.index(str.endIndex, offsetBy: -10)
let mySubstring = str[index...] // playground

// Suffix:
let index = str.index(str.endIndex, offsetBy: -10)
let mySubstring = str.suffix(from: index) // playground
let mySubstring = str.suffix(10) // playground

//Range in a string
let start: String.Index = str.index(str.startIndex, offsetBy: 7)
let end: String.Index = str.index(str.endIndex, offsetBy: -6)
let range: Range<String.Index> = start..<end
let mySubstring = str[range]  // play
```

### 186. Avoiding boilerplate code "required init"
```swift
open class BaseView: UIView {
   /**
    * Initiate
    */
   override public init(frame: CGRect = .zero) {
      super.init(frame: frame)
   }
   /**
    * Boilerplate
    */
   @available(*, unavailable) // This line avoids the requirement in subsequent subclasses etc
   public required init?(coder: NSCoder) {
      fatalError("init(coder:) has not been implemented")
   }
}
```
### 185. Selected or all selected

```swift
extension UITextInput {
    var selectedOrFullTextRange: UITextRange {
        return selectedTextRange // Return the selected text range if it exists
            ?? textRange( // If the selected text range does not exist, try to get the text range from the beginning of the document to the end of the document
                from: self.beginningOfDocument,
                to: self.endOfDocument)
            ?? UITextRange() // If the text range cannot be obtained, return an empty `UITextRange`
    }
}
```

### 184. Current scene
```swift
extension UIApplication {
    var cur rentScene: UIWindowScene? {
        connectedScenes // Get all the connected scenes
            .first { $0.activationState == .foregroundActive } // Find the first scene whose activation state is `.foregroundActive`
        as? UIWindowScene // Cast the scene to `UIWindowScene`
    }
}
```

### 183. Openable URL
`"https://google.com".isOpenableURL // true`
```swift
extension String {
    var isOpenableURL: Bool {
        guard let url = URL(string: self) else { return false } // Try to create a URL object from the string, return false if it fails
        guard url.scheme != nil else { return false } // Check if the URL has a scheme, return false if it does not have a scheme
        guard UIApplication.shared.canOpenURL(url) else { return false } // Check if the URL can be opened by the shared application, return false if it cannot be opened
        return true // Return true if the URL can be opened
    }
}
```

### 182. Weak wrapper:
let weakArr = ["a", "b", 2].wrapped
weakArr // Weak("a"), Weak("b"), Weak(2)
weakArr.unWrapped // a,b,2
```swift
public class Weak<T: AnyObject> {
    public weak var value: T?
    public init(_ value: T) {
        self.value = value
    }
}
typealias WeakArray = [Weak]
extension WeakArray where T == AnyObject{
   var wrapped: [Weak<T>] {
      map { Weak($0) }
   }
   var unWrapped: [T] {
      compactMap { $0.value }
   }
}
```

### 181. Clamped range:

```swift
public extension Comparable {
    func clamped(to limits: ClosedRange<Self>) -> Self {
        return min(max(self, limits.lowerBound), limits.upperBound)
    }
}
print(25.clamped(to: 0...20)) // 20
```

### 180. Create instances based on dynamic class types
```swift
let isSecure: Bool = false // or true
var baseType: NSTextField.Type {
   self.isSecure ? NSSecureTextField.self : NSTextField.self
}
let tf: NSTextField = self.baseType.init(frame: .zero) // Creates secure or normal textfield
```

### 170. Find parent view control
```swift
public var parentViewController: NSViewController? {
   sequence(first: self, next: \.nextResponder).compactMap({ $0 as? NSViewController }).first
}
```

### 169. Find where to insert something
```swift
extension Collection {
   /**
    * - Fixme: ⚠️️ add doc
    * - Remark: Slower than binary search
    * ## Examples:
    * let arr = ["a", "g", "r"]
    * let idx = arr.insertionIndex(of: "r", using: <)
    * rowData.insert("m", at: idx) // ["a", "g", "m", "r"]
    */
   func insertionIndex(of element: Self.Iterator.Element, using areInIncreasingOrder: (Self.Iterator.Element, Self.Iterator.Element) -> Bool) -> Index {
      firstIndex { !areInIncreasingOrder($0, element) } ?? endIndex
   }
}
/**
 * Binary Search
 */
extension RandomAccessCollection where Element: Comparable {
   func insertionIdx(of value: Element) -> Index {
    var slice: SubSequence = self[...] // Initialize a variable `slice` with a subsequence of the string
    while !slice.isEmpty { // Loop while the slice is not empty
        let middle = slice.index(slice.startIndex, offsetBy: slice.count / 2) // Get the middle index of the slice
        if value < slice[middle] { // Check if the value is less than the middle element of the slice
            slice = slice[..<middle] // If the value is less than the middle element, update the slice to the left half of the slice
        } else {
            slice = slice[index(after: middle)...] // If the value is greater than or equal to the middle element, update the slice to the right half of the slice
        }
    }
    return slice.startIndex // Return the start index of the slice
   }
}
```

### 168: let closure
Sometimes you want to store a method in a variable
```swift
typealias Message = (_ oldPSW: String, _ newPSW: String) -> String
let message: Message = { oldPSW, newPSW in
   "Change from old password: \(oldPSW) to new: \(newPSW)"
}
print(message("abc", "123")) // "Change from old password: abc to new: 123"
```

### 167: Collection over array:
- If you know you’re going to be working with other collection types, and you don’t want to incur the cost of allocating an array
- Usually, Array is a good currency type, but if you’re writing a parser or some other code where you expect to be handed an ArraySlice or something like that, you could do it the way you posted.
- This also allows for things like display(users:) to work with data types like Set, for example.
- In general it just allows display(users:) to be much more flexible.  I wouldn't say that it's something that you should always write "by default" instead of [User]/Array<User>, but if you find yourself creating display(users: [User]) and another version display(users: Set<User>) and another version display(users: ArraySlice<User>) (and so on), then just using some Collection<User> makes this much more flexible and reduces a lot of code.
- It can be useful for making functions more flexible, but also be careful with it, because certain assumptions are no longer valid:
- Here you just want to iterate over a collection, fine But if you want to display users, that collection should be sorted. Array is, Set isn't. It won't give an error at compile time, and it'll run fine, but your UI will be inconsistent (tests might fail?)
- Certainly. Hence the “don’t just default to the style just because.” Use it with purpose.

```swift
struct User {
    let firstName: String
}
func display(users: some Collection<User>) {
    for user in users {
        print(user)
    }
}
```

### 166: shorthand if-let syntax

```swift
var userName: String?
if let userName {
    // `userName` is non-optional here
}
```
### 165: large number separators

```swift
let bigNumber = 123_456_789
```

### 164. KeyPaths in closures
```swift
extension Int {
    var isEven: Bool { self.isMultiple(of: 2) }
}
let numbers = [1, 2, 3, 4, 5]
let evens = numbers.filter(\.isEven)
```

### 163. Use dump call to get more info
```swift
class Person {
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }

    var name: String
    var age: Int
}

let me = Person(name: "Vincent", age: 31)

dump(me)
```

### 162. Where condition in array iterating:
```swift
let numbers = [1, 2, 3, 4, 5]
for number in numbers where number.isMultiple(of: 2) {
    print(number)
}
```

### 161. Better actions for UIButtons
```swift
// ioS14+
var label: UILabel
var button: UIButton

var counter = 0 {
     didSet {
         label.text = "Counter: \(counter)"
     }
 }
 override func viewDidLoad() {
     super.viewDidLoad()
     button.addAction(UIAction { [weak self] _ in
         self?.counter += 1
     }, for: .touchUpInside)
 }
```

### 160. Using result to handle async calls:

```swift
func fetchData(_ completion: @escaping (Result<Data, Error>) -> Void) {
    /* ... */
}
fetchData { result in
    switch result {
    case .success(let data):
        // use the data
    case .failure(let error):
        // handle the error
    }
}
```

### 159. some isntead of generic
```swift
func handle(value: some Identifiable) {
    /* ... */
}
```
### 158. switch on tuples

```swift
switch (boolean1: boolean1, boolean2: boolean2, boolean3: boolean3) {
case (boolean1: true, _, boolean3: false):
    functionA()
case (_, boolean2: false, boolean3: true):
    functionB()
case (boolean1: true, boolean2: false, boolean3: false):
    functionC()
default:
    break
}
```

### 157. string or empty

```swift
extension Optional where Wrapped == String {
    var orEmpty: String {
        self ?? ""
    }
}

let optionalString = Bool.random() ? "Hello, world!" : nil

print(optionalString.orEmpty)
```

### 156. string is nil or empty
```swift
extension Optional where Wrapped == String {
    var isNilOrEmpty: Bool {
        self == nil || self == ""
    }
}
func handles(optionalString: String?) {
    if optionalString.isNilOrEmpty == false {
        // use `optionalString`
    }
}
```

### 155. Json string:
```swift
let json = #"{"name":"Vincent"}"#
```

### 154. Optional extension
```swift
extension String? {
    var orEmpty: String {
        self ?? ""
    }
}
```
### 153. Lazy let
```swift
class ViewController: UIViewController {
    private(set) lazy var subview: UIView = {
        let view = UIView()
        // configure `view`
        return view
    }()
}
```

### 152.  Safe way to return element at specified index

You can extend collections to return the element at the specified index if it is within bounds, otherwise nil.

```swift
extension Collection {
    subscript (safe index: Index) -> Element? {
        return indices.contains(index) ? self[index] : nil
    }
}

let cars = ["Lexus", "Ford", "Volvo", "Toyota", "Opel"]
let selectedCar1 = cars[safe: 3] // Toyota
let selectedCar2 = cars[safe: 6] // not crash, but nil
```

### 151. Tips for writing error messages

1. Say what happened and why
2. Suggest a next step
3. Find the right tone (If it’s a stressful or serious issue, then a silly tone would be inappropriate)

### 150. Split array by chunks of given size:

Great extension to split array by chunks of given size

```swift

extension Array {
    func chunk(_ chunkSize: Int) -> [[Element]] {
        return stride(from: 0, to: self.count, by: chunkSize).map({ (startIndex) -> [Element] in
            let endIndex = (startIndex.advanced(by: chunkSize) > self.count) ? self.count-startIndex : chunkSize
            return Array(self[startIndex..<startIndex.advanced(by: endIndex)])
        })
    }
}
```

### 149. Lightweight way to add content changing animation to UIView


Just invoke 🧙‍♂️ fadeTransition(_ duration: CFTimeInterval) by your view before you will apply a change.

```swift

extension UIView {
    func fadeTransition(_ duration: CFTimeInterval) {
        let animation = CATransition() // Create a new `CATransition` object
        animation.timingFunction = CAMediaTimingFunction(name: kCAMediaTimingFunctionEaseInEaseOut) // Set the timing function of the animation to ease in and ease out
        animation.type = kCATransitionFade // Set the type of the animation to fade
        animation.duration = duration // Set the duration of the animation to the specified duration
        layer.add(animation, forKey: kCATransitionFade) // Add the animation to the layer with the specified key
    }
}

```
Example:
```swift

label.fadeTransition(1)
label.text = "Updated test content with animation"

```

### 148. Remove dups
Clear way to return the unique list of objects based on a given key. It has the advantage of not requiring the Hashable and being able to return an unique list based on any field or combination.

```swift
extension Array {
    func unique<T:Hashable>(map: ((Element) -> (T)))  -> [Element] {
        var set: Set<T> = [] // Initialize an empty set of type `T`
        var arrayOrdered: [Element] = [] // Initialize an empty array of type `Element`
        for value in self { // Loop through each value in the sequence
            if !set.contains(map(value)) { // Check if the set does not contain the mapped value of the current value
                set.insert(map(value)) // If the set does not contain the mapped value, insert the mapped value into the set
                arrayOrdered.append(value) // Append the current value to the ordered array
            }   
        }
        return arrayOrdered // Return the ordered array
    }
}
```

### 147. Dispatch group
Let’s say you’ve got several long running tasks to perform. After all of them have finished, you’d like to run some further logic. You could run each task in a sequential fashion, but that isn’t so efficient - you’d really like the former tasks to run concurrently. DispatchGroup enables you to do exactly this.
```swift

let dispatchGroup = DispatchGroup()

for i in 1...5 {
    dispatchGroup.enter()
    Alamofire.request(url, parameters: params).responseJSON { response in
        //work with response
        dispatchGroup.leave()
    }
}

dispatchGroup.notify(queue: .main) {
    print("All requests complete")
}
```
In the above, all long running functions will perform concurrently, followed by the print statement, which will execute on the main thread.

Each call to enter() must be matched later on with a call to leave(), after which the group will call the closure provided to notify().

DispatchGroup has a few other tricks:

Instead of notify(), we can call wait(). This blocks the current thread until the group’s tasks have completed.
A further option is wait(timeout:). This blocks the current thread, but after the timeout specified, continues anyway. To create a timeout object of type DispatchTime, the syntax .now() + 1 will create a timeout one second from now.
wait(timeout:) returns an enum that can be used to determine whether the group completed, or timed out.

### 146. Asynchronous work in Playground.
Tell the playground it should continue running forever, otherwise it will terminate before the asynchronous work has time to hppen.

```swift
PlaygroundPage.current.needsIndefiniteExecution = true
```

### 145. Delegate naming:
your method names will should use `will`, `did`, and `should`

### 144. Protocol naming:

Protocols: Naming

If we are talking about naming the protocol itself:

Protocols that describe what something is should read as nouns (e.g. Collection).
Protocols that describe a capability should be named using the suffixes able, ible, or ing (e.g. Equatable, ProgressReporting).

### 143. Optional protocol methos:

```swift
// If you implement a protocol in Swift you must implement all its requirements. Optional methods are not allowed.
protocol CarDelegate {
    func engineDidStart()
    func carShouldStartMoving() -> Bool
    func carDidStartMoving()
    func engineWillStop()
    func engineDidStop()
}
// But there are a few tricks how to make some methods to be optionals:
// You can split them in two protocols, and adopt only needed one.
protocol CarMovingStatusDelegate {
    func carShouldStartMoving() -> Bool
    func carDidStartMoving()
}
protocol CarEngineStatusDelegate {
    func engineDidStart()
    func engineWillStop()
    func engineDidStop()
}
```

### 142. Result type without value to provide

```swift
enum Result<T> {
    case success(result: T)
    case failure(error: Error)
}
func login(with credentials: Credentials, handler: @escaping (_ result: Result<User>) -> Void) {
    // Two possible options:
    handler(Result.success(result: user))
    handler(Result.failure(error: UserError.notFound))
}
// login(with:) operation has user value to provide and default Result type fits perfectly here. But let’s imagine that your operation hasn’t got value to provide or you don’t care about it. Default Result type makes you to provide the result value any way.
// To fix this inconvenience you need to add extension and instantiate a generic with an associated value of type Void.
func login(with credentials: Credentials, handler: @escaping (_ result: Result<Void>) -> Void)
extension Result where T == Void {
    static var success: Result {
        return .success(result: ())
    }
}
// Now we can change our func login(with:) a bit, to ignore result success value if we don’t care about it.
func login(with credentials: Credentials, handler: @escaping (_ result: Result<Void>) -> Void) {
    // Two possible options:
    handler(Result.success)
    handler(Result.failure(error: UserError.notFound))
}
```

### 141. XCTUWrap
In Xcode 11 new assertion function has been added for use in Swift tests. XCTUnwrap asserts that an Optional variable’s value is not nil, returning the unwrapped value of expression for subsequent use in the test. It protects you from dealing with conditional chaining for the rest of the test. Also it removes the need to use XCTAssertNotNil(_:_:file:line:) with either unwrapping the value. It’s common to unwrap optional before checking it for a particular value so that’s really where XCTUnwrap() will come into its own.

```swift

struct Note {
    var id: Int
    var title: String
    var body: String
}
class NotesViewModel {
    static func all() -> [Note] {
        return [
            Note(id: 0, title: "first_note_title", body: "first_note_body"),
            Note(id: 1, title: "second_note_title", body: "second_note_body"),
        ]
    }
}
func testGetFirstNote() throws {
    let notes = NotesViewModel.all()
    let firstNote =  try XCTUnwrap(notes.first)
    XCTAssertEqual(firstNote.title, "first_note_title")
}
```

### 140. URL components
Dictionary of the URL's query parameters
```swift
extension URL {
    var queryParameters: [String: String]? {
        guard let components = URLComponents(url: self, resolvingAgainstBaseURL: false) else { return nil } // Try to create a URL components object from the URL string, return nil if it fails
        guard let queryItems = components.queryItems else { return nil } // Get the query items from the URL components, return nil if there are no query items
        var items: [String: String] = [:] // Initialize an empty dictionary of type `[String: String]`
        for queryItem in queryItems { // Loop through each query item
            items[queryItem.name] = queryItem.value // Add the query item name and value to the dictionary
        }
        return items // Return the dictionary of query parameters
    }
}
```

### 139. Numeric sum
Sum Function for Numeric Collection

```swift
extension Collection where Element: Numeric {
    func sum() -> Element {
        return self.reduce(0, +)
    }
}
[3, 4, 6].sum() // 13
[3.4, 6.2, 7.3].sum() // 16.9
```

### 138. Remove dups:

```swift
public extension Array where Element: Hashable {
    public mutating func removeDups() {
        self = unified()
    }
}
public extension Collection where Element: Hashable {
    public func unified() -> [Element] {
            return reduce(into: []) { // Reduce the sequence to an array, initialized with an empty array
                if !$0.contains($1) { // Check if the array does not contain the current element
                    $0.append($1) // If the array does not contain the current element, append the current element to the array
                }
            }
        }
}
var array = [1, 2, 3, 3, 2, 1, 4]
array.removeDups() // [1, 2, 3, 4]
```

### 137. Quality of service:
Apps and operations compete to use finite resources: memory, network interfaces, CPU, and so on. In order to remain responsive and efficient, the system needs to prioritize tasks and make intelligent decisions about when to execute them.

A quality of service (QoS) class allows you to categorize work to be performed by NSOperation, NSOperationQueue, NSThread objects, dispatch queues, and pthreads (POSIX threads). By assigning a QoS to work, you indicate its importance, and the system prioritizes it and schedules it accordingly.
```swift
// Work is virtually instantaneous.
DispatchQoS.userInteractive
// Work is nearly instantaneous, such as a few seconds or less.
DispatchQoS.userInitiated
// Work takes a few seconds.
DispatchQoS.default
// Work takes a few seconds to a few minutes.
DispatchQoS.utility
// Work takes significant time, such as minutes or hours.
DispatchQoS.background
```

### 136. One to many observer pattern:
The Observer design pattern defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically. The Observer pattern is essentially a publish-and-subscribe model in which the subject and its observers are loosely coupled. Communication can take place between the observing and observed objects without either needing to know much about the other. Cocoa implements the observer pattern in two ways: Notifications and Key-Value Observing (KVO).
```swift
// Observer Pattern
protocol Observer {
    var id : Int { get } // property to get an id
    func update<ObservableValue>(with newValue: ObservableValue)
}
protocol Observable {
    associatedtype ObservableValue // Declare an associated type `ObservableValue`
        var value : ObservableValue { get set } // Declare a variable `value` of type `ObservableValue`
        var observers : [Observer] { get set } // Declare a variable `observers` of type `[Observer]`
        func addObserver(observer: Observer) // Declare a method `addObserver` that takes an `Observer` object as a parameter
        func removeObserver(observer: Observer) // Declare a method `removeObserver` that takes an `Observer` object as a parameter
        func notifyAllObservers<ObservableValue>(with newValue: ObservableValue) // Declare a generic method `notifyAllObservers` that takes a new value of type `ObservableValue` as a parameter and notifies all observers
}
```

### 135. Mutating structs with a function call
If you need to modify the properties of your structure or enumeration within a particular method, you can opt in to mutating behavior for that method.
```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        x += deltaX
        y += deltaY
    }
}
var somePoint = Point(x: 1.0, y: 1.0)
somePoint.moveBy(x: 2.0, y: 3.0)
print("The point is now at (\(somePoint.x), \(somePoint.y))")
// Prints "The point is now at (3.0, 4.0)"
```

### 134. count where
The new count(where:) method:

```swift
let scores = [100, 80, 85]
let passCount = scores.count { $0 < 90 }
```

### 133. Class and instance name

```swift
String(describing: UIView.self) // "UIView"
String(describing: type(of: UILabel()))  // "UILabel"

```

### 132. Multiline string
```swift
"""
The red fox walked down the
street. It was not jumping the fence.
"""
```

### 131. Implicit and explicit getters
```swift
struct A {
   let data: Data
   var cellData: Data { data } // Wrong 🚫 (because it looks like its a variable)
}
struct A {
   let data: Data
   var getCellData: Data { data } // Correct ✅ (clearly indicates that it is a getter)
}
```

### 130. Clamping a value:
We can also do: `Swift.max(minVal, Swift.min(maxVal, val))`
```swift
/**
 * Clamp
 * ## Examples:
 * CGFloat.clamp(val: 44, min: 33, max: 38) // 38
 * - Parameters:
 *   - val: target value
 *   - min: no less
 *   - max: no more
 * - Returns: clamped value
 */
internal static func clamp(val: CGFloat, min: CGFloat, max: CGFloat) -> CGFloat {
   if val <= min { // Under
      return min
   } else if val >= max { // Over
      return max
   } else { // Between
      return val
   }
}
```

### 129. Data conversion
Data extension for conversion
```swift
extension Data {
  // Unarchive data into an object and return as type `Any`.
  public func convert() -> Any? {
     return NSKeyedUnarchiver.unarchiveObject(with: self)
  }
  // Converts an object into Data using NSKeyedArchiver
  public static func toData(object: Any) -> Data {
     return NSKeyedArchiver.archivedData(withRootObject: object)
  }
}
```

### 128. Using packages to test things quick
**Problem:**    
- Playground is nice but hit and miss sometimes
- Online swift compilers are iffy, and debug log is gnarly  
- Swift code in the terminal is awesome, but terminal isnt grat for typing code

**Solution:**  
1. Create a ScratchPad swift package
2. Drag it into xcode
3. Use unit-test to run xode

### 127. Change string with a range:

```swift
let input = "Hello, world"
let range = NSMakeRange(4, 8)
// To make that into a Swift Range instance you just need this single line of code:
let swiftRange = Range(range, in: input)
print(swiftRange.lowerBounds) // 0
print(swiftRange.upperBounds) // 10
input.replaceSubstring(range, "****")
print(input) // he****ld
```

### 126. Comparing two arrays where the item has a custom equality method
```swift
struct Item {
	let id: String
	let date: Int
}
extension Item {
    func isEqual(other: Item) -> Bool {
		return self.id == other.id
	}
}
typealias Items = [Item]

extension Array where Element == Item {
 	func isEqual(others: Items) -> Bool {
	    let intersection = self.filter { item in others.contains { $0.isEqual(other: item) } }
       return intersection.count == others.count && intersection.count == self.count // its important to assert count on both arrays
	}
}
let x: Items = [
	.init(id: "a", date: 0),
	.init(id: "b", date: 1),
	.init(id: "c", date: 2)
]
let y: Items = [
	.init(id: "a", date: 0),
	.init(id: "b", date: 1),
	.init(id: "c", date: 2)
]
let z: Items = [
	.init(id: "a", date: 7),
	.init(id: "d", date: 5),
	.init(id: "f", date: 2)
]
print(x.isEqual(others: y)) // true
print(x.isEqual(others: z)) // false
print(y.isEqual(others: z)) // false
```

### 125. Overriding super type method
Subclassing works even tho the Util method only knows about the base type
```swift
class A {
	func merge(item: A) {
		insert()
	}
	func insert() {
		Swift.print("A.insert")
	}
}
class B: A {
	override func insert() {
		Swift.print("B.insert")
        super.insert()
	}
}
class Util {
	static func merge(item: A) {
		item.merge(item: item)
	}
}
let item: A = B()
Util.merge(item: item)
// prints B.insert then A.insert
```

### 124. Assert that an array intersects another

```swift
let a = [3, 4]
let b = [2, 1, 3]
let intersection = a.filter { item in b.contains { $0 == item } }
print(intersection.count == a.count) // false -> its missing 4
```

## 123. Find the first VC in a VC hierarchy
Put this in a UIVC Extension. Usage: `let topAlertController: UIAlertController? = topMostVC?.firstMatch()`
```swift
/**
 * Traverses the entire VC hierarchy downwards and returns the first match
 * - Parameter type: Class type to match
 */
public func firstMatch<T: UIViewController>(type: T.Type? = nil) -> T? {
  self.children.first {
     $0.firstMatch(type: type) != nil
  } as? T
}
```

## 122. Add a comment to infoplist
Because infoplist is complicated, and there is a lot of knowledge attached to choices of which key/val that are stored in infoplist
```yml
<!-- A Boolean value indicating whether the app may open the original document from a file provider, rather than a copy of the document.  -->
```

## 121. Random range:
```swift
Int.random(in: 8...44) // random int between 8 and 44
(8..<44).randomElement() ?? 8 // random int between 8 and 44
String(Int.random(in: (1000..<9999))) // 4 digit code like 4413, 9999 etc
```

### 120. Struct vs Class
- If it’s basically a bag of data, use a struct
- If it has a lifecycle, use a class

### 119. Switch on tuples:
Great for testing groups of cases
```swift
let state = (a: true, b: false, c: "123")
switch state {
case (true, _, _): print("a is true")
case (_, _, "123"): print("c has 123 text")
case (_, _, _): print("Did not find a match")
}
```

### 118: Golden path:
When coding with conditionals, the left-hand margin of the code should be the "golden" or "happy" path. That is, don't nest if statements. Multiple return statements are OK. The guard statement is built for this.

```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {
  guard let context = context else {
    throw FFTError.noContext
  }
  guard let inputData = inputData else {
    throw FFTError.noInputData
  }
  // Use context and input to compute the frequencies
  return frequencies
}
```

### 117: Shorthand syntax for unwrapping optionals
For Swift 5.7 and up
```swift
if let textContainer {
  // do many things with textContainer
}

```

### 116: for-where Loops

When the entirety of a for loop’s body would be a single if block testing a condition of the element, the test is placed in the where clause of the for statement instead.

```swift
// instead of:
for item in collection {
  if item.hasProperty {
    // ...
  }
}
// do this:
for item in collection where item.hasProperty {
  // ...
}
// for-where Loops
// When the entirety of a for loop’s body would be a single if block testing a condition of the element, the test is placed in the where clause of the for statement instead
for item in collection where item.hasProperty {
  // ...
}
for item in collection {
  if item.hasProperty {
    // ...
  }
}
```

### 115: String from enum case:

Also works even if a case is assigned a string

```swift
enum CarType {
case ford, volvo, fiat
}
String(describing: CarType.ford) // ford
```

### 114: Autoclousure:
```swift
func printTest1(_ result: () -> Void) {
    print("Before")
    result()
    print("After")
}
printTest1({ print("Hello") })
// With auto-closure:
func printTest2(_ result: @autoclosure () -> Void) {
    print("Before")
    result()
    print("After")
}

printTest2(print("Hello"))
```

### 113: Using where in guards
- Using where is the same as using comma, but can be more human readable.
- Also a reminder to use where in other places. like for-loops. Which is easy to forget etc
```swift
currentRequest?.getValue { [weak self] result in
guard let user = result.okValue where result.errorValue == nil else { // Check if the `okValue` of the result is not nil and the `errorValue` of the result is nil
    self?.showRequestError(result.errorValue) // If the `okValue` is nil or the `errorValue` is not nil, show the request error
    self?.isPerformingSignUp = false // Set the `isPerformingSignUp` flag to false
    return // Return from the method
}
self?.finishSignUp(user) // If the `okValue` is not nil and the `errorValue` is nil, finish the sign up process with the user object
}
```

### 112. Add property wrapper to user default

```swift
@propertyWrapper
 struct UserDefault<T: Codable> {
    let key: String // Declare a constant `key` of type `String`
    let defaultValue: T // Declare a constant `defaultValue` of type `T`
     init(_ key: String, defaultValue: T) {
        self.key = key // Set the `key` property to the specified key
        self.defaultValue = defaultValue // Set the `defaultValue` property to the specified default value
     }
     var wrappedValue: T {
        get {
            if let data = UserDefaults.standard.object(forKey: key) as? Data, // Try to get the data from the user defaults for the specified key
                let user = try? JSONDecoder().decode(T.self, from: data) { // Try to decode the data as an object of type `T`
                return user // If the decoding succeeds, return the decoded object
            }
            return  defaultValue // If the decoding fails or the data does not exist, return the default value
        } set {
            if let encoded = try? JSONEncoder().encode(newValue) { // Try to encode the new value as JSON data
                UserDefaults.standard.set(encoded, forKey: key) // Set the encoded data in the user defaults for the specified key
            }
        }
     }
 }
enum GlobalSettings {
    @UserDefault("user", defaultValue: User(name:"",pass:"")) static var user: User
}
// Example User model confirm Codable
struct User:Codable {
    let name:String
    let pass:String
}
// How to use it
// Set value
 GlobalSettings.user = User(name: "Ahmed", pass: "Ahmed")
// GetValue
print(GlobalSettings.user) // Ahmed
```

### 111. Use defer in init instad of didSet
Invoke didSet when property’s value is set inside init context. Apple's docs specify that: "Property observers are only called when the property’s value is set outside of initialization context." Defer can change situation:
```swift
class AA {
    var propertyAA: String! {
        didSet {
            print("Function: \(#function)")
        }
    }
    init(propertyAA: String) {
        self.propertyAA = propertyAA
    }
}
class BB {
    var propertyBB: String! {
        didSet {
            print("Function: \(#function)")
        }
    }
    init(propertyBB: String) {
        defer {
            self.propertyBB = propertyBB
        }
    }
}
let aa = AA(propertyAA: "aa")
let bb = BB(propertyBB: "bb")
```

### 110. Curried calls that throw
- This is an effort to reduce methods in an API.
- Here we attach the network call in a closure block.
- Usually we would duplicate the database api calls for network.
- Like: `insertAndSync`, `deleteAndSync`, `deleteAllAndSync` and 10 more etc
- Instead we attach the network call in a parameter to the database API
- Bonus: We can read the error from the POV of the caller if network produce one etc
- Bonus: The database API stays the same. If we don't add the complete param, everything is unchanged
```swift
typealias Complete = () throws -> Void
let defaultComplete: Complete = {}

class Database {
   static var values: [String: String] = [:]
   static func insert(uuid: String, value: String, _ complete: Complete = {}) throws {
      print("Insert: \(uuid) value: \(value)")
      values[uuid] = value
      try complete()
   }
   static func delete(uuid: String, _ complete: Complete = {}) throws {
      print("Delete: \(uuid)")
      if values[uuid] == nil {
         throw NSError.init(domain: "Err, delete - value at uuid: \(uuid) does not exists", code: 0)
      }
      values.removeValue(forKey: uuid)
      try complete()
   }
}
class Network {
   static func sync() throws {
      Swift.print("sync")
      if Bool.random() {
         throw NSError.init(domain: "Err, sync - unable to sync", code: 0)
      }
   }
}
// insert
do { try Database.insert(uuid: "1234", value: "abc", Network.sync) }
catch { Swift.print(error.localizedDescription) }
// delete
do { try Database.delete(uuid: "1234", Network.sync) }
catch { Swift.print(error.localizedDescription) }
// delete
do { try Database.delete(uuid: "1234", Network.sync) }
catch { Swift.print(error.localizedDescription) }
// Prints:
// Insert: 1234 value: abc
// sync
// Delete: 1234
// sync
// The operation couldn’t be completed. (Err, sync - unable to sync error 0.)
// Delete: 1234
// The operation couldn’t be completed. (Err, delete - value at uuid: 1234 does not exists error 0.)
```

### 109. Wonders of OOP (Object oriented programming)
Neat trick to hock into subclass functionality
```swift
class A {
    func update() {
        insert() // Calls first B.insert not A.insert
    }
    func insert() {
        print("A.insert()")
    }
}
class B: A {
    override func insert() {
        print("B.insert()")
        super.insert()
    }
}
B().update() // Call B.insert() then A.insert() is called after
```

### 108. Overriding extension methods that has (parameter or return) with protocol types
- We prefix the protocol with @objc because we need to return this protocol type in "objc-override" calls
- We do this so that we can group related code in extensions. Rather than have classes with too much code

```swift
@objc protocol TextKind {
   var text: String { get set }
}
class A {}
extension A {
   @objc func getText() -> TextKind? {
      print("A")
      return nil
   }
}
class B: A {}
extension B {
  override getText() {
     print("B")
     return nil
  }
}
_ = A().getText() // A
_ = B().getText() // B
```

### 107. Weakify closure variable
Usually we can use `[weak self]` in closurs, but not if its being attached to an event variable. Here is a workaround:
```swift
Service.onRemoteEvent = { data in
   guard let self = Optional(self) else { return } // Weakify
   update(self) // Use self safely here
}
```

### 106. Attaching a subtype to a protocol conformance
Setting the protocol type to CBManager directly wont compile. But using T with a type will
```swift
protocol StateUpdatable {
    associatedtype T: CBManager // Declare an associated type `T` that must conform to `CBManager`
    var manager: T { get } // Declare a variable `manager` of type `T`
}
class Central: StateUpdatable {
   var manager: CBCentralManager = ... // CBCentralManager extends CBManager
}
class peripheral: StateUpdatable {
   var manager: CBPeripheralManager = ... // CBPeripheralManager extends CBManager
}
```

### 105. Initiating an instance from an abstract class type
```swift
let baseType: NSTextField.Type = isSecure ? NSSecureTextField.self : NSTextField.self
let tf: NSTextField = baseType.init(frame: .init(origin: .zero, size: .init(width: 200, height: 24)))
```

## 104. Mouse position in an NSView for macOS
```swift
extension NSView {
   /**
    * Mouse point
    * - Note: window.mouseLocationOutsideOfEventStream can also work as relativeToWin point
    */
   var mousePoint: CGPoint {
    let relativeToWin = self.window?.convertPoint(fromScreen: NSEvent.mouseLocation) ?? .zero // Try to convert the mouse location from the screen coordinate system to the window coordinate system, or use `.zero` if the window is nil
    return self.convert(relativeToWin, from: nil) // Convert the point from the window coordinate system to the view's coordinate system and return it
   }
}
```

### 103. Differentiating on typealias tuple
A simple concept to diff on tuples. Useful for making mixed search queries etc.
```swift
typealias A = (type: String, id: String)
typealias B = (type: String, label: String)
let x: Any = (type: "", label: "")
if x is A {
    print("A")
} else if x is B {
    print("B")
}
// Prints "B"
```

### 102. Overriding rawValue enum case
Great for supporting localization efforts etc.
```swift
enum Test: CaseIterable {
   case a,b,c
}
extension Test {
   init?(rawValue: String) {

   }
   var rawValue: String {
      switch self {
      case .a: return "A"
      case .b: return "B"
      case .c: return "C"
      }
   }
}
Test.allCases.forEach {
   print($0.rawValue) // "A", "B", "C"
}
```

### 101. Reduce into:
Reduce trickery: (https://stackoverflow.com/a/43429063/5389500)
```swift
[0, 1, 1, 0].reduce(into: "") { $0 += $1 } // "0110"
...
```

### 100. Traversing view hierarchy
Traverses the entire UIView hierarchy downwards and collects views that are of specific type

```swift
public func descendants<T>(type: T.Type? = nil) -> [T] {
   self.subviews.flatMap { $0.subviews.isEmpty ? [$0 as? T].compactMap { $0 } : $0.descendants(type: type) }
}
```

## 99. Remove first item in an array
```swift
extension Array {
   /**
    * Remove first item that match some criteria
    * - Note: fileprivate is used because private won't work because we are in the Array scope
    * ## Examples:
    * uuids.removeFirst(closure: { $0 == uuid })
    */
   @discardableResult fileprivate mutating func removeFirst(closure: (Element) -> Bool) -> Element? {
    guard let i: Int = self.firstIndex (where: { closure($0) }) else { return nil } // Try to get the index of the first element in the array that satisfies the closure, return nil if it does not exist
    return self.remove(at: i) // Remove the element at the specified index and return it
   }
}
```

## 98. Assert internet:
```swift
/**
 * Checks to see if internet is reachable
 * ## Example:
 * Self.checkNetwork { Swift.print("Net: \($0)") }
 * - Fixme: Add result? to print error etc? or just throw error?
 * - Fixme: Add semaphore with timeout as well
 */
static func checkNetwork(completionHandler: @escaping (_ internet: Bool) -> Void) {
   DispatchQueue.main.async {
    let url: URL = .init(string: "https://www.apple.com/")! // Create a URL object from the specified string
    let request: URLRequest = .init(url: url) // Create a URL request object from the URL object
    let task = URLSession.shared.dataTask(with: request) {data, response, error in // Create a data task with the URL request
        if error != nil { // Check if there is an error
            Swift.print("Error:  \(String(describing: error))") // If there is an error, print the error message
            completionHandler(false) // Call the completion handler with a `false` value
        } else if let httpResponse = response as? HTTPURLResponse { // If there is no error, check if the response is an HTTP URL response
            if httpResponse.statusCode == 200 { completionHandler(true) } // If the status code is 200, call the completion handler with a `true` value
            else { print("Status-code: \(httpResponse.statusCode)") } // If the status code is not 200, print the status code
        }
    }
    task.resume() // Start the data task
   }
}
```

## 97. Comparing class types
```swift
class A: X {}
class B: X {}
class X {}

let b: X = B()
print(type(of: b.self))

let types: [X.Type] = [A.self, B.self]
print(types[1]) // B
print(type(of: b)) // B
print(type(of: b) == types[1]) // true
```

## 96. Instantinating from an array of class types
```swift
class BaseType {
   required init(frame: CGRect) {}
}
class A: BaseType {}
class B: BaseType {}
class C: BaseType {}
let types: [BaseType.Type] = [A.self, B.self, C.self]
types.forEach {
   let instance = $0.init(frame: .zero)
}
```

## 95. Calling apple async methods:
- If your asynchronous work needs to be waited for, you don’t have much of a choice but to mark your current code as also being async so that you can use await as normal. However, sometimes this can result in a bit of an “async infection” – you mark one function as being async, which means its caller needs to be async too, as does its caller, and so on, until you’ve turned one error into 50.
- In this situation, you can create a dedicated Task to solve the problem. We’ll be covering this API in more detail later on, but here’s how it would look in your code:
```swift
func doAsyncWork() async {
    print("Doing async work")
}
func doRegularWork() {
    Task {
        await doAsyncWork()
    }
}
doRegularWork() // Tasks like this one are created and run immediately. We aren’t waiting for the task to complete, so we shouldn’t use await when creating it.
```

## 94. Converting one dictionary type to another
```swift
let dict: [String, Any] = [:]
let newDict: [String: String] = dict.compactMapValues { $0 as? String }
```

## 93. Closure that throws
Sometimes it's useful to be able to throw through a closure:
```swift
func someMethod() throws -> String { "works" }
var closure: () throws -> Void {}
closure {
   print(try someMethod())
}
try closure() // works
```

## 92. Remove first where
```swift
extension Array {
   /**
    * Remove first item that match some criteria
    */
   @discardableResult mutating func removeFirst(closure: (Element) -> Bool) -> Element? {
      guard let i: Int = self.firstIndex (where: { closure($0) }) else { return nil }
      return self.remove(at: i)
   }
}
```

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
      let shared = SomeClass(isTestMode: isTestMode) // Temp variable
      _shared = shared // Set permanent variable
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

Or of a custom length with: (Change 4 to get longer strings)

```bash
cat /dev/urandom | LC_CTYPE=C tr -dc 'a-zA-Z0-9' | head -c 4; echo
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
    weak var _self = self // Declare a weak reference to `self`
        func nestedFunction(result : Bool) { // Declare a function `nestedFunction` that takes a boolean parameter `result`
            _self?.anotherInstanceMethod() // Call the `anotherInstanceMethod` method on the weak reference to `self`
        }
        functionExpectingClosure(nestedFunction) // Call the `functionExpectingClosure` function with the `nestedFunction` closure as a parameter
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
    if any0 == nil && any1 == nil { return true } // Check if both `any0` and `any1` are nil, return true if they are
    guard
        let equatable0 = any0 as? Self, // Try to cast `any0` to `Self`
        let equatable1 = any1 as? Self // Try to cast `any1` to `Self`
    else { return false } // If either cast fails, return false

    return equatable0 == equatable1 // Return the result of the equality comparison between `equatable0` and `equatable1`
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
enum DataReceivedError: Error { case offline, denied } // Declare an enumeration `DataReceivedError` that conforms to `Error` and has two cases: `offline` and `denied`
typealias OnDataReceived = (Result<String, DataReceivedError>) -> Void // Declare a type alias `OnDataReceived` for a closure that takes a `Result` object with a `String` value and a `DataReceivedError` error as parameters and returns `Void`
var onDataReceived: OnDataReceived = { result in // Declare a variable `onDataReceived` of type `OnDataReceived` and assign a closure that takes a `Result` object with a `String` value and a `DataReceivedError` error as a parameter
    guard value = try? result.get() else { print("Err: \(result.error?.localizedDescription)") } // Try to get the value from the `Result` object, print the error message if it fails
    print("content: \(value)") // Print the value if it succeeds
}
onDataReceived(.success("some content"), nil) // Call the `onDataReceived` closure with a `success` case and a `nil` error value
onDataReceived(.failure(.offline)) // Call the `onDataReceived` closure with a `failure` case and an `offline` error value
```

## 81. Handy UIAlertController shortcuts:
Simplifies calling UIAllertController
```swift
// Extension:
extension UIAlertController {
    // Create a static function `alertOnError` that takes an error object and an optional closure as parameters and returns a UIAlertController object with an error title and message, and an OK button action that calls the closure if it is not nil
    static func alertOnError(_ error: Swift.Error, handler: ((UIAlertAction?) -> Void)? = nil) -> UIAlertController {
            let alert = UIAlertController(title: "Error", message: error.localizedDescription, preferredStyle: UIAlertController.Style.alert)
            alert.addAction(UIAlertAction(title: "OK", style: UIAlertAction.Style.default, handler: handler))
            return alert
        }

    // Create a static function `alertOnErrorWithMessage` that takes a message string and an optional closure as parameters and returns a UIAlertController object with an error title and the specified message, and an OK button action that calls the closure if it is not nil
    static func alertOnErrorWithMessage(_ message: String, handler: ((UIAlertAction?) -> Void)? = nil) -> UIAlertController {
            let alert = UIAlertController(title: "Error", message: message, preferredStyle: UIAlertController.Style.alert)
            alert.addAction(UIAlertAction(title: "OK", style: UIAlertAction.Style.default, handler:handler))
            return alert
        }

    // Create a static function `alertWithMessage` that takes a message string and an optional closure as parameters and returns a UIAlertController object with an alert title and the specified message, and an OK button action that calls the closure if it is not nil
    static func alertWithMessage(_ message: String, handler: ((UIAlertAction?) -> Void)? = nil) -> UIAlertController {
            let alert = UIAlertController(title: "Alert", message:message, preferredStyle: UIAlertController.Style.alert)
            alert.addAction(UIAlertAction(title: "OK", style: UIAlertAction.Style.default, handler:handler))
            return alert
        }
}
// Usage:
let err = NSError(domain: "err", code: 0) // Create an NSError object with the specified domain and code
let closure = { (_ action: UIAlertAction?) in print(action) } // Declare a closure that takes an optional UIAlertAction object as a parameter and prints it
UIAlertController.alertOnError(err, closure).present() // Call the `alertOnError` static function on the UIAlertController class with the `err` object and the `closure` closure as parameters, and present the resulting UIAlertController object
UIAlertController.alertOnErrorWithMessage("Uh oh", closure).present() // Call the `alertOnErrorWithMessage` static function on the UIAlertController class with the `"Uh oh"` message string and the `closure` closure as parameters, and present the resulting UIAlertController object
UIAlertController.alertWithMessage("Open the bay doors?", closure).present() // Call the `alertWithMessage` static function on the UIAlertController class with the `"Open the bay doors?"` message string and the `closure` closure as parameters, and present the resulting UIAlertController object
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
    // Declare a function `find` that takes an ID parameter and returns the first element in the array that has a matching ID, or nil if no element is found
    func find(_ id: Element.ID) -> Element? {
            self.first { $0.id == id }
        }

    // Declare a function `findIndex` that takes an ID parameter and returns the index of the first element in the array that has a matching ID, or nil if no element is found
    func findIndex(_ id: Element.ID) -> Int? {
            self.firstIndex { $0.id == id }
        }

    // Declare a mutating function `remove` that takes an ID parameter and removes all elements from the array that have a matching ID
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
// Declare a computed property `dict` that returns a dictionary representation of the object, or nil if the object cannot be encoded as JSON data or deserialized as a dictionary
public var dict: [String: Any]? {
        guard let data = try? JSONEncoder().encode(self) else { return nil } // Try to encode the object as JSON data, return nil if it fails
        return (try? JSONSerialization.jsonObject(with: data, options: .allowFragments)).flatMap { $0 as? [String: Any] } // Try to deserialize the JSON data as a dictionary, return nil if it fails
    }

// Declare an initializer `init` that takes a dictionary parameter and throws an error if the object cannot be decoded from the dictionary
public init(dict: [String: Any]) throws {
        self = try JSONDecoder().decode(Self.self, from: JSONSerialization.data(withJSONObject: dict)) // Try to decode the object from the dictionary, throw an error if it fails
    }
}
struct Job: Codable { let number: Int, name: String, client: String } // Declare a struct `Job` that conforms to `Codable` and has three properties: `number`, `name`, and `client`
let job: Job = .init(number: 1234, name: "Awards Ceremony", client: "ACME Productions") // Create a `Job` object with the specified properties
let dict: [String: Any] = job.dict ?? [:] // Convert the `Job` object to a dictionary, or an empty dictionary if the conversion fails
let clone = try? Job(dict: dict) // Try to create a new `Job` object from the dictionary, or nil if the creation fails
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
let mirror = Mirror(reflecting: instance) // Create a mirror object for the specified instance
let keysWithValues = mirror.children.compactMap { (label: String?, value: Any) -> (String, Any)? in // Get the children of the mirror object, filter out any children with a nil label, and return a tuple of the label and value for each child
        guard let label = label else { return nil } // If the label is nil, return nil
        return (label, value) // Otherwise, return a tuple of the label and value
    }
return Dictionary(uniqueKeysWithValues: keysWithValues) // Create a dictionary from the array of tuples, using the labels as keys and the values as values
}
```

### 69. Loop through struct / class properties:
> This can also be done with mirroring
```swift
extension Encodable {
    var dictionary: [String: Any]? { // Declare a computed property `dictionary` that returns a dictionary representation of the object, or nil if the object cannot be encoded as JSON data or deserialized as a dictionary
            guard let data = try? JSONEncoder().encode(self) else { return nil } // Try to encode the object as JSON data, return nil if it fails
            return (try? JSONSerialization.jsonObject(with: data, options: .allowFragments)).flatMap { $0 as? [String: Any] } // Try to deserialize the JSON data as a dictionary, return nil if it fails
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
⚠️️ this might not work 😅
```swift
func isEqual(a: [String], b: [String]) -> Bool {
   guard a.count == b.count else { return false } // Check if count is the same
   return !a.contains { item in // Does it have a case where item isn't found in the other array
      !b.contains { otherItem in // Does it have a case where item isn't found in the other array
         item != otherItem
      }
   }
}
print(isEqual(a: ["1","2","3"], b: ["1","2","4"])) // false
print(isEqual(a: ["1","2","3"], b: ["1","2","3"])) // true
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
    // Declare a function `filterDuplicates` that returns an array of unique elements from the original array, preserving their order
    func filterDuplicates() -> Array<Element> {
            var set = Set<Element>() // Create an empty set to store unique elements
            var filteredArray = Array<Element>() // Create an empty array to store the filtered elements
            for item in self { // Iterate over each element in the original array
                if set.insert(item).inserted { // Try to insert the element into the set, and append it to the filtered array if it is unique
                    filteredArray.append(item)
                }
            }
            return filteredArray // Return the filtered array
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
    let w: CGFloat = size.width // Get the width of the size
    let h: CGFloat = size.height // Get the height of the size
    if (w / h) > ratio { // Check if the width is wider than the ratio allows
        return CGSize(height: h * ratio, height: h) // If so, return a new size with the height scaled to match the ratio and the original height
    } else if (w / h) < ratio { // Check if the height is taller than the ratio allows
        return CGSize(width: w, height: w * ratio) // If so, return a new size with the width scaled to match the ratio and the original width
    } else {
        return CGSize(width: w, height: h) // Otherwise, return the original size
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
    prefix(1).capitalized // Get the first character of the string and capitalize it
    + dropFirst() // Drop the first character of the string and return the rest
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
    let currentStyle = Apperance() // Create an instance of the `Apperance` class and assign it to the `currentStyle` constant
    if case .Dark = currentStyle { // Check if the `currentStyle` is `.Dark`
        return true // If so, return `true`
    } else if case .Light = currentStyle { // Check if the `currentStyle` is `.Light`
        return false // If so, return `false`
    } else { // If the `currentStyle` is neither `.Dark` nor `.Light`
        fatalError("Not supported") // Raise a fatal error with the specified message
    }
   }
}
```

### 48: Generic typealias
```swift
// Warning: ⚠️️ Generics does not work when overriding functions in extensions
typealias Parser<A> = (String) -> [(A, String)]
func parse<A>(strin gToParse: String, parser: Parser)
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
- There is also `@available(*, deprecated)` if something doesn't have a replacement API call etc
- You can also rename func-params: `@available(*, deprecated, renamed: "init(type:title:value:)")`
```swift
@available(*, deprecated, renamed: "newMethodName") // You can also point to new class : "UIAlertController.createAlert"
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
    let start = DispatchTime.now() // Get the current time
    closure() // Call the closure
    let end = DispatchTime.now() // Get the current time again
    let diff = end.uptimeNanoseconds - start.uptimeNanoseconds // Calculate the difference between the two times in nanoseconds
    return Double(diff) / 1_000_000_000 // Convert the difference to seconds and return it
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
    Swift.print("start") // Print "start"
    DispatchQueue.global().async { // Dispatch a task asynchronously to a global queue
            DispatchQueue.concurrentPerform(iterations: 4) { index in // Perform a task concurrently for the specified number of iterations
                Swift.print("\(index)") // Print the index
                sleep((1..<3).randomElement()!) // Wait for a random number of seconds between 1 and 3
            }
            onComplete() // Call the completion handler
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
for y in (0..<height) { // Iterate over each row of the grid
    for x in (0..<width) { // Iterate over each column of the grid
        let index: Int = y * width + x // Calculate the index of the current cell
        print("index: \(index)") // Print the index of the current cell
    }
}
// flattening:
var x = 0 // Initialize the x-coordinate to 0
var y = 0 // Initialize the y-coordinate to 0
while x < width && y < height { // Loop while the x-coordinate is less than the width and the y-coordinate is less than the height
    let index: Int = y * width + x // Calculate the index of the current cell
    print("i: \(index)") // Print the index of the current cell
    x += 1 // Increment the x-coordinate
    if x == width { y += 1; x = 0 } // If the x-coordinate is equal to the width, increment the y-coordinate and reset the x-coordinate to 0
}
```

## 39. Accounting for iPhoneX notch
```swift
// Override the `viewDidAppear` method to adjust the view's frame to fit the screen's bounds, taking into account the safe area insets
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    view.frame = UIScreen.main.bounds.inset(by: view.safeAreaInsets)
}

// Override the `viewDidLoad` method to set the view to a custom `View` instance with a zero frame
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
// Define a nested `Color` struct with nested `Text` and `UI` structs, each containing static properties for various colors
struct Color {
    struct Text {
        static let header: UIColor = .white // The color for header text
        static let button: UIColor = .systemBlue // The color for button text
        static let description: UIColor = .lightGray // The color for description text
        static let paragraph: UIColor = .gray // The color for paragraph text
        static let title: UIColor = .white // The color for title text
    }
    struct UI {
        struct Foreground {
            static let primary: UIColor = .darkGray // The primary foreground color
            static let secondary: UIColor = .gray // The secondary foreground color
        }
        struct Background {
            static let primary: UIColor = .gray // The primary background color
            static let secondary: UIColor = .lightGray // The secondary background color
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
    let dispatchGroup = DispatchGroup() // Create a new dispatch group
    dispatchGroup.enter() // Enter the dispatch group
    functionOne { dispatchGroup.leave() } // Call `functionOne` and leave the dispatch group when it completes
    dispatchGroup.wait() // Wait for `functionOne` to complete, with a reasonable timeout
    dispatchGroup.enter() // Enter the dispatch group again
    functionTwo { dispatchGroup.leave() } // Call `functionTwo` and leave the dispatch group when it completes
    dispatchGroup.wait() // Wait for `functionTwo` to complete, with a reasonable timeout
    dispatchGroup.enter() // Enter the dispatch group again
    functionThree { dispatchGroup.leave() } // Call `functionThree` and leave the dispatch group when it completes
    dispatchGroup.wait() // Wait for `functionThree` to complete, with a reasonable timeout
    dispatchGroup.notify(queue: .main) { // Notify the main queue when all tasks are completed
        Swift.print("all done") // Print "all done"
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
        if let sublist = element as? [Self.Iterator.Element] { // Check if the element is an array
            results += sublist.recursiveFlatmap() // If so, recursively call `recursiveFlatmap` on the sublist and append the results to the `results` array
        } else if let element = element as? T { // Otherwise, check if the element is of type `T`
            results.append(element) // If so, append the element to the `results` array
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
class SomeTableViewCell: UITableViewCell {} // Define a `SomeTableViewCell` class that inherits from `UITableViewCell`
extension SomeTableViewCell {
    static let cellReuseIdentifier: String = "\(SomeTableViewCell.self)" // Define a static property `cellReuseIdentifier` that returns the class name as a string
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
let result1 = users.map { user in // Map over each user in the `users` array
    return user.accounts.map { account in // Map over each account in the user's `accounts` array
        return account.subscriptions.map { subscription in // Map over each subscription in the account's `subscriptions` array
            return subscription.type // Return the subscription type
        }
    }
}.flatMap{ $0 }.flatMap { $0 } // Flatten the nested arrays into a single array of subscription types
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
DispatchQueue.global(qos: .background).async { // Dispatch a task asynchronously to a global queue with a background quality of service
    array.forEach { i in // Iterate over each element in the `array`
        sleep(1) // Sleep for 1 second
        DispatchQueue.main.async { // Dispatch a task asynchronously to the main queue
            Swift.print("i: \(i)") // Print the current element of the `array`
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

⚠️️ write result example with the try catch inline trick, and remove the bellow etc

⚠️️ improve this: ⚠️️

```swift
enum Result<Value> {
    case success(Value)
    case failure(Swift.Error)
}

func start(_ completionHandler: @escaping (Result<Any>) -> Void) -> FBSDKGraphRequestConnection{
    return start() { (_, response, error) in // Call the `start` method with a completion handler that takes a tuple of optional `URLResponse`, optional `Error`, and no return value
        switch (response, error) { // Use a tuple pattern matching to check the response and error
        case (.some(let result), .none): // If the response is not `nil` and the error is `nil`
            completionHandler(Result(value: result)) // Call the completion handler with a `Result` instance containing the response

        case (.none, .some(let error)): // If the response is `nil` and the error is not `nil`
            completionHandler(Result(error: error)) // Call the completion handler with a `Result` instance containing the error

        case (.none, .none), (.some, .some): // If both the response and error are `nil` or both are not `nil`
            preconditionFailure("Unexpected State") // Raise a runtime error
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
    let btn: UIButton = .init(type: .system) // Create a new `UIButton` instance with the system type
    btn.backgroundColor = bgColor // Set the background color of the button to `bgColor`
    btn.setTitle(text, for: .normal) // Set the title of the button to `text` for the normal state
    btn.titleLabel?.font =  .systemFont(ofSize: 12) // Set the font of the button's title label to a system font with size 12
    btn.frame = .init(x:00, y:y, width:100, height:50) // Set the frame of the button to a new `CGRect` instance with the specified values
    btn.addTarget(self, action: Selector(action), for: .touchUpInside) // Add a target-action pair for the `.touchUpInside` control event
    self.addSubview(btn) // Add the button as a subview of the current view
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
} else if let thirdItem = (2 < arr.count ?  arr[2] : nil) {
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
