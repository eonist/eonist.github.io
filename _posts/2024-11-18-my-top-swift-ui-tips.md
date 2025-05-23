My top swiftUI tips and tricks<!--more-->

### 65. Overridable default body in a protocol

To use a protocol that extends View with a default body that adds a view to the background, you can use an associated type for the background view. Here's how you can structure your protocol and implement it:

```swift
protocol CustomBackgroundView: View {
    associatedtype BackgroundView: View
    var backgroundView: BackgroundView { get }
}

extension CustomBackgroundView {
    var body: some View {
        content
            .background(backgroundView)
    }
    
    @ViewBuilder
    private var content: some View {
        // Your default content here
        Text("Default Content")
    }
}
```
Now you can implement this protocol in your custom views:
```swift
struct MyCustomView: CustomBackgroundView {
    var backgroundView: some View {
        Color.blue
    }
    
    // Optional: Override the content if needed
    var body: some View {
        VStack {
            Text("Custom Content")
        }
        .background(backgroundView)
    }
}
```

This approach allows you to define a default body in the protocol extension while still providing flexibility for custom implementations. The associatedtype for BackgroundView allows you to use some View in the conforming types without getting an error.

If you need more customization, you can add more associated types or methods to the protocol. Remember that when using this approach, you'll need to specify the background view for each conforming type.

### 64. Avoid initiated state bug

When using `State(wrappedValue:)` in a SwiftUI view's initializer, the state value persists when reloading the parent view due to SwiftUI's view identity and state management system. To avoid this behavior, you can use one of the following approaches:

1. Use a binding instead of local state:
   Instead of creating local state in the child view, pass a binding from the parent view. This ensures that the child view's state is always in sync with the parent.

   ```swift
   struct ChildView: View {
       @Binding var text: String
       
       var body: some View {
           Text(text)
       }
   }

   struct ParentView: View {
       @State private var parentText = ""
       
       var body: some View {
           ChildView(text: $parentText)
       }
   }
   ```

2. Use the `.id()` modifier:
   Apply the `.id()` modifier to the child view in the parent view, using a value that changes when you want to reset the state. This forces SwiftUI to recreate the child view with fresh state[3][4].

   ```swift
   struct ParentView: View {
       @State private var parentText = ""
       
       var body: some View {
           ChildView(initialText: parentText)
               .id(parentText) // This will recreate ChildView when parentText changes
       }
   }
   ```

3. Use `@StateObject` with an observable object:
   For more complex state management, consider using `@StateObject` with an observable object that can be reset or updated from the parent view[3].

   ```swift
   class ChildViewModel: ObservableObject {
       @Published var text: String
       
       init(initialText: String) {
           self.text = initialText
       }
   }

   struct ChildView: View {
       @StateObject private var viewModel: ChildViewModel
       
       init(initialText: String) {
           _viewModel = StateObject(wrappedValue: ChildViewModel(initialText: initialText))
       }
       
       var body: some View {
           Text(viewModel.text)
       }
   }
   ```

4. Use `@Binding` with a computed property:
   Combine `@Binding` with a computed property to reset the state when the parent view changes[6].

   ```swift
   struct ChildView: View {
       @Binding var parentText: String
       @State private var localText: String
       
       var text: String {
           get { localText }
           set {
               localText = newValue
               parentText = newValue
           }
       }
       
       init(parentText: Binding) {
           _parentText = parentText
           _localText = State(initialValue: parentText.wrappedValue)
       }
       
       var body: some View {
           TextField("Enter text", text: $text)
       }
   }
   ```

These approaches help ensure that the child view's state is properly managed and reset when necessary, avoiding unexpected persistence of state values when reloading the parent view[1][3][4][6].
 

### 63. Inline include / exclude
Decide to include chained calls or not

```swift
extension View {
   /**
    * Inline includer helper
    * #Examples:
    * someView.include(flag: false) { $0.styleWrapper }
    * - Parameters:
    *   - flag: to include or not
    *   - content: content to include
    * - Returns: self or content
    */
   @ViewBuilder public func include(
      flag: Bool,
      @ViewBuilder content: (_ view: Self) -> some View
   ) -> some View {
      if flag {
         content(self)
      } else {
         self
      }
   }
}
```

### 62. Avoid unused let warning in xcode

Prefix the let with @unused

```swift
@unused let someValue = ...
```

### 61. Image modifier 

Just like viewmodifier but for images

```swift
import SwiftUI

public protocol ImageModifier {
   associatedtype Body: View
   func body(image: Image) -> Body
}

extension Image {
   public func modifier<M: ImageModifier>(_ modifier: M) -> M.Body {
      modifier.body(image: self)
   }
}
```

### 60. Finding built in font sizes

- Problem: Built in fonts like .body and .headline etc are great. But they are finicky when dealing with fixed font sizes. Fixed designs. etc. 
- Solution. Use system fonts with hard coded sizes. 

Find the equivilent
```swift
#Preview {
    @Previewable @Environment(\.dynamicTypeSize) var dynamicTypeSize
    let _ = {
        let bodyFont = UIFont.preferredFont(forTextStyle: .body) // 17
        let fontSize = bodyFont.pointSize
        Swift.print("fontSize: \(fontSize)")
        // Ensure we are testing standard size
        Swift.print("dynamicTypeSize: \(dynamicTypeSize)") // large (large means defaul)
    }()
}
```

### 59. Relative font-size / line-height

- Problem: Consistent line-heights for text. Font size does not equal height
- Solution: Make the frame use fixed fontSize + multiplier

In SwiftUI, you can base the height of a view relative to the font size by using the font modifier to get the font size and then applying that size to the height of the view. Here's a step-by-step guide on how to achieve this:

Define the Font Size: Use the font modifier to set the font size for a text view.
Calculate the Height: Use the font size to calculate the height of the view.
Apply the Height: Use the frame modifier to set the height of the view based on the font size.

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            Text("Hello, World!")
                .font(.system(size: 24)) // Step 1: Define the font size
                .background(Color.yellow)
                .frame(height: fontSizeToHeight(fontSize: 24)) // Step 3: Apply the height

            Text("Another Text")
                .font(.system(size: 36)) // Step 1: Define the font size
                .background(Color.green)
                .frame(height: fontSizeToHeight(fontSize: 36)) // Step 3: Apply the height
        }
    }

    // Step 2: Calculate the height based on the font size
    func fontSizeToHeight(fontSize: CGFloat) -> CGFloat {
        return fontSize * 1.5 // You can adjust the multiplier as needed
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

### 58. Mocking keychain in preview

- Problem: Keychain is finicky in preview. 
- Solution: Use mock env variable

This approach uses mock data and a simulated Keychain wrapper specifically for previews. It provides a clean way to test your UI without attempting to access real Keychain services. The mock implementation can be shared across different views that require Keychain access.

```swift
struct SecureDataView_Previews: PreviewProvider {
    static var previews: some View {
        // Use mock data directly
        SecureDataView(data: "Preview Value")
            .environment(\.keychainStorage, MockKeychainStorage())
    }
}
class MockKeychainStorage: ObservableObject {
    @Published var data: String = ""
    
    func get(_ key: String) -> String? { return data }
    func set(_ value: String, forKey key: String) { data = value }
}
```
**Pros**

- Simplest approach
- No additional dependencies
- Works consistently across all Xcode versions

**Cons**

- Doesn't test actual Keychain behavior
- Requires maintaining mock implementation

### 57. Generic typealias

```swift
typealias ViewClosure<T: View> = (_ flag: Bool) -> T

// This typealias defines a closure that takes a Bool parameter and returns a generic type T that conforms to the View protocol. You can use this typealias in your SwiftUI code like this:

struct ContentView: View {
    let viewClosure: ViewClosure<AnyView>
    
    var body: some View {
        viewClosure(true)
    }
}

```

Note that you'll need to wrap your view in AnyView when using this typealias, which may have some performance implications4. If you're using this typealias frequently, consider alternatives like creating a custom view or using @ViewBuilder for more idiomatic SwiftUI code

**Use Type Erasure**
If you need a non-generic alias, you can use type erasure by returning an AnyView:

```swift
typealias ViewClosure = (_ flag: Bool) -> AnyView
```

Wrapping your concrete view in AnyView lets you satisfy the typealias while “hiding” the underlying view type. Keep in mind that type erasure may incur a slight performance cost compared to using generics.

In summary, while you might wish to use an opaque return type directly in a typealias, Swift’s current design doesn’t allow it. You must either parameterize your alias generically or resort to type erasure.

### 56. Generic ViewModifier

Style Text with any text modifier

```swift
struct CompositeView<TextModifier: ViewModifier>: View {
    var textModifier: TextModifier
    init(textModifier: TextModifier) {
        self.textModifier = textModifier
    }
    var body: some View {
        Text("Sample Text")
            .modifier(textModifier)
    }
}

struct HeaderTextModifier: ViewModifier {
    func body(content: Content) -> some View {
        content.foregroundColor(.blue)
    }
}

let view = CompositeView(textModifier: HeaderTextModifier())
```

### 55. Binding extension:

In this example, the CredentialSelectionView uses toggle switches to select the credential kind. The extension we created allows us to bind the CredentialKind state directly to the toggles, providing a clean and intuitive way to interact with the enum values

```swift
// Define an enum to represent different types of credentials.
enum CredentialKind {
    case username
    case password
    case apiKey
}

// Extend Binding for values of type CredentialKind to create boolean bindings.
extension Binding where Value == CredentialKind {

    // Create a binding that is true when the credential is of type 'username'.
    func isUsername() -> Binding<Bool> {
        Binding<Bool>(
            get: { self.wrappedValue == .username },
            set: { newValue in
                // If the boolean binding is set to true, update the wrapped value to 'username'.
                if newValue {
                    self.wrappedValue = .username
                }
            }
        )
    }
    
    // Create a binding that is true when the credential is of type 'password'.
    func isPassword() -> Binding<Bool> {
        Binding<Bool>(
            get: { self.wrappedValue == .password },
            set: { newValue in
                // If the boolean binding is set to true, update the wrapped value to 'password'.
                if newValue {
                    self.wrappedValue = .password
                }
            }
        )
    }
    
    // Create a binding that is true when the credential is of type 'apiKey'.
    func isApiKey() -> Binding<Bool> {
        Binding<Bool>(
            get: { self.wrappedValue == .apiKey },
            set: { newValue in
                // If the boolean binding is set to true, update the wrapped value to 'apiKey'.
                if newValue {
                    self.wrappedValue = .apiKey
                }
            }
        )
    }
}
```
This extension provides convenient methods to check and set the CredentialKind value using boolean bindings. Here's how you might use this in a SwiftUI view:
```swift
struct CredentialSelectionView: View {
    @State private var credentialKind: CredentialKind = .username
    
    var body: some View {
        Form {
            Toggle("Username", isOn: $credentialKind.isUsername())
            Toggle("Password", isOn: $credentialKind.isPassword())
            Toggle("API Key", isOn: $credentialKind.isApiKey())
            
            Text("Selected credential: \(credentialKind.description)")
        }
    }
}

extension CredentialKind: CustomStringConvertible {
    var description: String {
        switch self {
        case .username: return "Username"
        case .password: return "Password"
        case .apiKey: return "API Key"
        }
    }
}
```

### 54. Preview hack for github action issue

Github action has issues using #Preview and @Previewable at times. By fencing the preview as bellow, there fatal error goes away

```Swift
#if canImport(SwiftUI) && compiler(>=6.0) // ⚠️️ fix for ga bug
#Preview {
    @Previewable @State var text: String = ""
}
#endif
```

### 53. ForEach indentifed by

The .identified(by:) method is used when your collection doesn't conform to Identifiable, but you can provide a keypath to a property that uniquely identifies each element. This method returns an IdentifierValuePairs collection, which SwiftUI can use to efficiently update and manage the views.
Here's a more concrete example:

```swift
struct Ocean {
    let name: String
    let area: Double
}

let oceans = [
    Ocean(name: "Pacific", area: 165250000),
    Ocean(name: "Atlantic", area: 106460000),
    Ocean(name: "Indian", area: 70560000)
]

struct ContentView: View {
    var body: some View {
        List {
            ForEach(oceans.identified(by: \.name)) { ocean in
                Text(ocean.name)
            }
        }
    }
}
```

### 52. Manaing multiple sheets
This approach allows you to manage multiple sheets using a single sheet modifier, making your code more maintainable and scalable. It also solves the issue of sheets not updating properly on the first presentation, which can occur when using multiple sheet modifiers
```swift
// Define an enum for your sheet types:
enum SheetType: Identifiable {
    case sheetA
    case sheetB
    var id: Self { self }
}
// Create a state variable to track the active sheet:
@State private var activeSheet: SheetType?
// Use the sheet(item:) modifier to present the sheets:
.sheet(item: $activeSheet) { sheetType in
    switch sheetType {
    case .sheetA:
        SheetAView()
    case .sheetB:
        SheetBView()
    }
}
// Trigger the sheet presentation by setting the activeSheet state:
Button("Show Sheet A") {
    activeSheet = .sheetA
}

Button("Show Sheet B") {
    activeSheet = .sheetB
}
```

### 51. Button or Image with gesture in list?

You can use both a Button in a List and an Image with onTapGesture in SwiftUI, but using a Button is generally recommended for better accessibility and built-in functionality. Here's how you can implement a button in a List:

```swift
List {
    Button(action: {
        // Your action here
    }) {
        HStack {
            Text("Button Text")
            Spacer()
            Image(systemName: "chevron.right")
        }
    }
    .buttonStyle(PlainButtonStyle())
}
```

1. Buttons automatically adapt their visual style to match different containers and contexts.
2. They provide better accessibility support out of the box.
3. They handle different types of interactions (tap, click, remote selection) across various platforms.

If you encounter issues with the entire row being tappable, you can use the `.contentShape(Rectangle())` modifier to control the tappable area[2]:

```swift
Button(action: {
    // Your action here
}) {
    HStack {
        Text("Button Text")
        Spacer()
    }
}
.contentShape(Rectangle())
.buttonStyle(PlainButtonStyle())
```

Using an Image with onTapGesture is a valid alternative, especially when you need more control over tap behaviors or want to combine different gesture types[3]. However, it may require additional work to achieve the same level of accessibility and visual feedback as a Button.

### 50. Measuring view height with a callback
Quick and easy way to measure height of views. 
```swift
struct HeightMeasuringModifier: ViewModifier {
    let callback: (CGFloat) -> Void
    func body(content: Content) -> some View {
        content
            .background(
                GeometryReader { geometry -> Color in
                    DispatchQueue.main.async {
                        callback(geometry.size.height)
                    }
                    return Color.clear
                }
            )
    }
}
extension View {
    func getHeight(_ callback: @escaping (CGFloat) -> Void) -> some View {
        self.modifier(HeightMeasuringModifier(callback: callback))
    }
}
#Preview {
    Text("Measure my height")
        .padding()
        .background(Color.yellow)
        .getHeight { height in
            print("The height of the view is: \(height)")
        }
}
```

### 49. Styling with viewbuilder
Custom view builders that encapsulate styling logic:

```swift
// Define the TextStyle struct
struct TextStyle {
    let font: Font
    let color: Color
}
struct StyledText: View {
    let text: String
    let style: TextStyle
    
    var body: some View {
        Text(text)
            .font(style.font)
            .foregroundColor(style.color)
    }
}
#Preview {
    StyledText(text: "Hello", style: TextStyle(font: .headline, color: .blue))
}
```

### 48. Optional binding
This can also be achived with using .constant(false / true)
```swift
Struct SomeView: View {
    var isPresented: Binding<Bool>?
    var body: some View {
        isPresented?.wrappedValue = false
    }
}
struct OtherView: View {
    @State var isPresented: Bool = true
    var body: some View {
        SomeView(isPresented: nil)
        // SomeView(isPresented: isPresented)
    }
}
```

### 47. Detect if sheet is presented for iOS
A way to detect if sheet is presented. 
```swift
@Environment(\.presentationMode) var presentationMode
print(presentationMode.wrappedValue.isPresented) // sheet is presented or not 
```

### 46. Detect if in preview
- This code snippet checks if the current process is running in Xcode's preview mode.
- If it is, it executes the code meant for preview mode.
- Otherwise, it runs the code intended for normal execution.
```swift
if ProcessInfo.processInfo.environment["XCODE_RUNNING_FOR_PREVIEWS"] == "1" {
    // Code to run in preview mode
} else {
    // Code to run outside of preview mode
}
```
Extension:
```swift
extension ProcessInfo {
   /**
    * ## Examples
    * if ProcessInfo.isRunningInPreviewMode {
    *    print(" Code specific to preview mode")
    * }
    */
   static var isRunningInPreviewMode: Bool {
      return processInfo.environment["XCODE_RUNNING_FOR_PREVIEWS"] == "1"
   }
}
```

### 45. Detect all changes in ObservableObject 
- It is possible to just do [val1, val2] in onChange as well
- We could also make it Equatable. and to onChange on the entire object
```swift
public class OffsetStore: ObservableObject {
   @Published public var outerY: CGFloat = 0
   @Published public var innerY: CGFloat = 0
   @Published public var headerHeight: CGFloat = 0
   public init(outerY: CGFloat = 0, innerY: CGFloat = 0, headerHeight: CGFloat = 0) {
      self.outerY = outerY
      self.innerY = innerY
      self.headerHeight = headerHeight
   }
}
struct Parent: View {
    @StateObject internal var offset = OffsetStore()
}
struct Child: View {
    @ObservedObject internal var offset: OffsetStore
    init(offset: OffsetStore) {
         self._offset = .init(initialValue: offset)
    }
    var body: some View {
        self
        .onReceive(offset.objectWillChange) { // detects all changes
            DispatchQueue.main.async {
                Swift.print("normalizedYOffset: \(offset.normalizedYOffset)")
            }
        }
    }
}

```

### 44. ViewHook
Add callback to manipulate a view
```swift
typealias ListHook = (AnyView) -> AnyView
struct SomeView: View {
    let listHook: ListHook?
    init(listHook: ListHook? = nil) {
        self.listHook = listHook
    }
    var body: some View {
        @ViewBuilder var view: some View {
            if let listHook = self.listHook {
            listHook(AnyView(list))
            } else {
                list
            }
        }
        return view
    }
}
```

### 43. @Previewable
@Previewable is a new macro introduced in Xcode 16 for SwiftUI Previews. It allows developers to use dynamic properties inline in previews, making it easier to create interactive and state-dependent previews without the need for wrapper views

- Enables the use of @State and other property wrappers directly within #Preview blocks 
- Simplifies the creation of previews for views with bindings or state-dependent behavior 
- Automatically generates the necessary wrapper code to make state work correctly in previews 
```swift
#Preview {
    @Previewable @State var text = ""
    TextField("Enter text", text: $text)
}
```
This macro significantly reduces boilerplate code and allows for more compact and interactive SwiftUI previews

### 42. Reacting to all changes in an ObservableObject:
- Use `.onReceive(model.objectWillChange)` to detect any change in the ObservableObject.
- Note: Make sure the variables in MyModel are set in dispatch main async call. Or else there might be race conditions
```swift
struct ContentView: View {
    @StateObject private var model = MyModel()

    var body: some View {
        VStack {
            Slider(value: $model.value1, in: 0...100)
            Slider(value: $model.value2, in: 0...100)
        }
        .padding()
        .onReceive(model.objectWillChange) {
            print("A value in the model changed: \(model.value1), \(model.value2)")
        }
    }
}
```
### 41. Using some View in a protocol

By using associatedtype Output: View, you allow the conforming types to specify their own Output type, which is what enables the use of some View in the implementation.
This approach provides flexibility while maintaining type safety and allowing for complex view hierarchies within the body property

```swift
// To use some View in a protocol for SwiftUI views, you can define the protocol like this:
protocol CustomViewProtocol: View {
    associatedtype Output: View // View is the default generic, avoiding needing to add it in the struct
    var someView: Self.Output { get }
}
// This approach allows you to create custom views that conform to the protocol Here's how you can use it:
struct MyCustomView: CustomViewProtocol {
    var body: some View {
        someView
    }
    var someView: some View {
        EmptyView()
    }
}
```

### 40: Type erase with @ViewBuilder
Using group and AnyView are other ways to achive the same thing. Check with copilot for pros and cons for each solution.
```swift
func getContent(flag: Bool) -> some View {
    @ViewBuilder var content: some View {
        if flag {
            Text("Text")
        } else {
            Button("Button")
        }
    }
    return content.background(Color.green)
}
```

### 39: SizeObserver:
```swift
class SizeObserver: ObservableObject {
    @Published var previousSize: CGSize = .zero
    @Published var currentSize: CGSize = .zero
    
    func updateSize(_ newSize: CGSize) {
        if newSize != currentSize {
            previousSize = currentSize
            currentSize = newSize
            print("Size changed from \(previousSize) to \(currentSize)")
        }
    }
}

struct ContentView: View {
    @StateObject private var sizeObserver = SizeObserver()
    
    var body: some View {
        GeometryReader { geometry in
            Color.clear
                .onAppear {
                    sizeObserver.updateSize(geometry.size)
                }
                .onChange(of: geometry.size) { newSize in
                    sizeObserver.updateSize(newSize)
                }
        }
    }
}
```

### 38. SizeTracker:
```swift
import SwiftUI

struct SizeTracker: ViewModifier {
   @State private var previousSize: CGSize = .zero
   @State private var currentSize: CGSize = .zero
   var onSizeChange: (CGSize, CGSize) -> Void
   
   func body(content: Content) -> some View {
      content
         .background(
            GeometryReader { geometry in
               Color.clear
                  .onAppear {
                     currentSize = geometry.size
                  }
                  .onChange(of: geometry.size) { oldSize, newSize in
                     previousSize = currentSize
                     currentSize = newSize
                     onSizeChange(previousSize, currentSize)
                  }
            }
         )
   }
}
extension View {
   func trackSize(onChange: @escaping (CGSize, CGSize) -> Void) -> some View {
      self.modifier(SizeTracker(onSizeChange: onChange))
   }
}
// Usage
struct ContentView: View {
   var body: some View {
      Text("Hello, World!")
         .frame(width: 200, height: 100)
         .trackSize { oldSize, newSize in
            if oldSize != newSize {
               print("Size changed from \(oldSize) to \(newSize)")
            }
         }
   }
}
```

### 37. Passing read-and-write-data downstream with environment object
- Read and write environment objects
- Changes to the variable triggers view update to parents and children
- Avoids "parameter drilling" (passing variables to every level of the view hierarchy)
- @EnvironmentObjectis more convenient than ObservedObject but requires careful management to avoid runtime crashes.
- Changing child views to new views, requires reapplying the environment variable etc

```swift
class SharedData: ObservableObject {
    @Published var value = "Initial value" // state variable
}

struct ParentView: View {
    @StateObject private var sharedData = SharedData() // onChange will update on var mutation from child

    var body: some View {
        VStack {
            Text("Parent: \(sharedData.value)")
            ChildView().environmentObject(sharedData)
        }
    }
}

struct ChildView: View {
    @EnvironmentObject var sharedData: SharedData

    var body: some View {
        VStack {
            Text("Child: \(sharedData.value)")
            Button("Modify Data") {
                sharedData.value = "Modified by child" // modify origin object
            }
        }
    }
}
```

### 36. Passing read-only-data downstream with environment variables

- Environment values are read-only for child views but can be modified in the origin parent view.
- Changes to Environment values trigger view updates.
- Avoids parameter drilling (passing variables to every level of the view hierarchy)

```swift
struct ParentView: View {
    @Environment(\.horizontalSizeClass) var sizeClass
     var body: some View {
        GrandChildView()
            .environment(\.horizontalSizeClass, sizeClass) // overrides downstream
            
    }
}
struct ChildView: View {
    var body: some View {
        GrandChildView()  
    }
}
struct GrandChildView: View {
    @Environment(\.horizontalSizeClass) var sizeClass
    var body: some View {
       print(sizeClass) // .regular (this is received from parent-view)
    }
}
// You can also create your own environment keys. 
private struct MyEnvironmentKey: EnvironmentKey {
    static let defaultValue: String = "Default Value"
}
extension EnvironmentValues {
    // @Environment(\.myEnvironmentKey) var sizeClass
    // .environment(\.horizontalSizeClass, "New Value")
    var myEnvironmentKey: String {
        get { self[MyEnvironmentKey.self] }
        set { self[MyEnvironmentKey.self] = newValue }
    }
}
```

### 35. implicit vs explicit return of EmptyView in @ViewBuilder
1. This snippet uses a guard statement to check if string can be assigned to str. If string is nil, it returns an EmptyView(), effectively rendering nothing. If string is not nil, it proceeds to return a Text view displaying the value of str.
```swift
@ViewBuilder func test() {
    guard let str = string else { 
        return EmptyView() 
    }
    return Text(str)
}
```

2. This snippet uses an if let statement to check if string can be assigned to str. If string is not nil, it returns a Text view displaying the value of str. However, unlike the first snippet, if string is nil, it does nothing and implicitly returns an EmptyView() due to the behavior of @ViewBuilder.
```swift
@ViewBuilder func test() {
    if let str = string { 
        return Text(str)
    }
}
```

### Key Differences
Explicit vs Implicit Return: The first snippet explicitly returns an EmptyView() when the condition fails, making the control flow clearer. The second snippet relies on the implicit return of EmptyView() by @ViewBuilder when no other views are returned, which might be less obvious to someone reading the code.

Readability and Intent: The explicit return of EmptyView() in the first snippet makes it clear that the function intentionally renders nothing under certain conditions. The second snippet assumes the reader understands the behavior of @ViewBuilder with conditional statements.

### Conclusion
While both snippets aim to conditionally render a Text view based on whether string is nil, they differ in their approach to handling the case where string is nil. The first snippet explicitly handles this case by returning an EmptyView(), whereas the second snippet relies on the implicit behavior of @ViewBuilder. Depending on coding style preferences and readability concerns, one may choose either approach.


### 34. nonmutating
Here's a simple example of using nonmutating in a property setter within a Swift property wrapper. This example demonstrates how to use nonmutating to allow the state variable to be modified without changing the instance of the property wrapper itself.

The nonmutating keyword in the setter allows the wrappedValue to be updated without changing the instance of ExamplePropertyWrapper. This is useful when you want to modify the internal state while keeping the property wrapper instance itself unchanged.

```swift
@propertyWrapper
public struct ExamplePropertyWrapper {
   private var value: Int // The stored value

   public var wrappedValue: Int { // The property wrapper's wrapped value
      get { value } // Getter for the value
      nonmutating set { // Setter for the wrapped value, marked as nonmutating
         value = newValue // Update the stored value
      }
   }

   public init(wrappedValue: Int) {
      self.value = wrappedValue // Initialize the stored value
   }
}
```
 

### 33. Whats the difference between initialValue and wrappedValue for a State variable?
There is no difference, they are the same. Apple shipped both and can't remove one because of ABI compatibility.

### 32. Easy way to double check sizes
- Set the simulator to 1x size. This can be ipad, mac or iphone. 
- Hit cmd + shift + 4 Now you can drag and measure with the printscreen ruler

### 31. Using Accessibility inspector with swiftui

In recent xcode releases this has stopped working like it used to. To get it working. Start a UITest session. And add a sleep(sec: 120) call somewhere. the inspector seem to work only when the app is live in the UITest session. Not in regular simulator mode. (works for iPhone, for iPad it gets things a bit wrong, misplacements etc)

### 30. Using NSAttributedText in swiftui
If you have existing NSAttributedString instances that you need to use in SwiftUI, you can convert them to AttributedString using the initializer that accepts an NSAttributedString:

```swift
let nsAttributedString = NSAttributedString(string: "Your text", attributes: [.foregroundColor: UIColor.red])
let attributedString = AttributedString(nsAttributedString)
```

### 29. Call accessibilityIdentifier indirectly
Because everything is a chain. One misplaces accessibiliytIdentifer can cause the app to crash in strange places. With no way of figuring out which id causes the issues. 

A solution is to call it indirecttly so it can be turned on and off and print a log of where it causes the crash. Here is an example of such a method: 

⚠️️ And sometimes XCode caches accessibilityIdentifiers somewhere it should not. So only a xcode rest will fix it. Erasing all data in the derivedData folder and simulator can also work

```swift
public func accessIdentifier(_ id: String) -> some View { 
    Swift.print("accessIdentifier - id:  \(id)")
    return self.accessibilityIdentifier(id) // return self to debug where things crash
}
```

### 28. Injecting dimiss into child view

Inspired by: https://www.swiftbysundell.com/articles/dismissing-swiftui-modal-and-detail-views/ and https://stackoverflow.com/a/74449402/5389500

This code lets you call the parent dismiss call from child. Sometimes useful for NavStacks inside popover sheets etc. Injecting the dimiss directly might cause crashes / infinite loops. Even when passing dismiss as DismissAction, so we call it indirectly with a function `callAsFunction`.

It might also be possible to use presentation mode: https://nilcoalescing.com/blog/UsingTheDismissActionFromTheSwiftUIEnvironment/

```swift
struct ParentView: View {
    @Environment(\.dismiss) private var dismiss
    ...
    ChildView(dismiss: dismiss.callAsFunction)
}
struct childView: View {
    var dismiss: (() -> Void)?
    ...
    dimiss?()
}
```

### 27. Dealing with Invalid frame dimension (negative or non-finite)

`var someVal: CGfloat = otherVal > 0 ? otherVal : 0`

### 26. UITests in swiftUI
When setting accessivilityIdentifier to swiftui containers. Prepend with 
```swiftui
.accessibilityElement(children: .contain) // this is key for setting access-id to the container and not the last child etc
.accessibilityIdentifier("some-id")
```

### 25. AnyView and @ViewBuilder

Using AnyView to embed different views in a sheet can create strange errors in Simulator. Such as  "unknown context"  at AnyViewStorage  error: llmd.

Instead limit the usage of @ViewBuilder if you don't have to.

### 24. ForEachIndex 
Iterate over view elements (Remember to return the view)

`ForEachIndex([1,2,3]) { i in Text("\(i)") }`

```swift
struct ForEachIndex<Data, Content>: View where Data: RandomAccessCollection, Content: View {
   var data: Data
   var content: (Int) -> Content
   init(_ data: Data, _ content: @escaping (Int) -> Content) {
      self.data = data
      self.content = content
   }
   var body: some View {
      ForEach(Array(data.enumerated()), id: \.offset) { index, _ in
         content(index)
      }
   }
}
```
### 23. Grouping styles
Sometimes you dont need to make a ViewModifier
```swift
/**
 * TextStyle
 */
extension Text {
   /**
    * Style for brand text
    * ## Examples:
    *  Text("\(String(text.prefix(2)).capitalized)")
    *     .brandIconTextStyle
    */
   var brandIconTextStyle: some View {
      self
         .multilineTextAlignment(.center)
         .foregroundColor(.whiteOrBlack.opacity(0.6))
         .font(.system(size: 16))
         .fontWeight(.bold)
   }
}
```

### 22. No need to mark preview structs with debug:
To be extra clear, you DO NOT need to wrap your preview providers in #if DEBUG conditionals. They are removed from your production build. Ref: https://stackoverflow.com/a/60463426

```swift
#if DEBUG // this is not needed
#Preview {
     MyView()
        .previewDevice("iPhone 12 Pro")
        .environment(\.sizeCategory, .large)
}
#endif // this is not needed
```

### 21. ForEachElement
Avoids the need for hashable, index is used as id `ForEachElement(["a","b","c"]) { text($0) }`
```swift
struct ForEachElement<Data, Content>: View where Data: RandomAccessCollection, Content: View {
   var data: Data
   var content: (Data.Element) -> Content
   
   var body: some View {
      ForEach(Array(data.enumerated()), id: \.offset) { _, element in
         content(element)
      }
   }
}
```

### 20. ForEachEnumerated
This method pairs each element in the collection with its index, allowing you to iterate over both the index and the element simultaneously.
```swift
struct EnumeratedForEach<Data, Content>: View where Data: RandomAccessCollection, Data.Element: Hashable, Content: View {
    var data: Data
    var content: (Int, Data.Element) -> Content

    var body: some View {
        ForEach(Array(data.enumerated()), id: \.element) { index, element in
            content(index, element)
        }
    }
}
```
 Data and Content. Data is the collection of items you want to iterate over, and Content is the type of view you want to generate for each item. The content closure takes the index and the element as parameters, allowing you to use both in your view.

 ```swift
struct ContentView: View {
    let items = ["Apple", "Banana", "Cherry"]

    var body: some View {
        VStack {
            EnumeratedForEach(data: items) { index, item in
                Text("Item \(index +  1): \(item)")
            }
        }
    }
}
```

### 19. Rebinding a local scoped variable
We can also do this via a binding extension that takes a closure. 
```swift
// Loop over items in a ForEach here
 let isSelected: Binding<Bool> = Binding( get: { $selectedIdx.wrappedValue == i }, set: { _ in $selectedIdx.wrappedValue = i })
 // Create a button and inject binding to apply selected state here
```

### 18. Padding extension
Instead of this: 
```swift
// Adds different padding values to all four sides
Text("Hello, SwiftUI!")
	.padding(.top, 20)
    .padding(.bottom, 10)
    .padding(.leading, 5)
    .padding(.trailing, 15)
```

Do this:

```swift
extension View {
    func padding(_ edgeLengths: [Edge.Set: CGFloat]) -> some View {
        var modifiedView = self
        
        for (edge, length) in edgeLengths {
            modifiedView = modifiedView.padding(edge, length)
        }
        
        return modifiedView
    }
}

// Usage:

Text("Hello, SwiftUI!")
	.padding([
		.leading: 20,
		.trailing: 40,
		.top: 10,
		.bottom: 30
	])
```

### 17. Rebinding:
Here is an example where we change a state by rebinding it. (It is a great tool to use in many cases, when wiring up a complex UI)

```swift
@State private var columnVisibility: NavigationSplitViewVisibility = .all
@Binding var isColumnVisible: Bool = Binding( get: { columnVisibility == .all }, set: { columnVisibility =  $0 ? .all : .doubleColumn })
$isColumnVisible.wrappedValue.toggle() // toggles the columnVisibility state
```

### 16. Simple user default wrapper:
```swift
class Prefs: ObservableObject {
    @Published
    var showCopyright: Bool = UserDefaults.standard.bool(forKey: "showCopyright") {
        didSet {
            UserDefaults.standard.set(self.showCopyright, forKey: "showCopyright")
        }
    }
}
@ObservedObject var prefs: Prefs
@EnvironmentObject var prefs: Prefs
```

### 15. Container views
"Container-views" are fundamental building blocks in SwiftUI. Here is how you make them:
```swift
struct ContainerView<Content: View>: View {
    let content: Content
    init(@ViewBuilder content: () -> Content) {
        self.content = content()
    }
    var body: some View {
        content
    }
}
// Starting from Swift 5.4, Swift can automatically create the 'init' function. This means we can use 'resultBuilder' for properties that are stored.
struct AmazingContainerView<Content: View>: View {
    @ViewBuilder
    let content: Content
    var body: some View {
        content
    }
}
```

### 14. Type erasing with group:
In SwiftUI, you can use conditions to decide which components to show. But, because of the way SwiftUI works, you have to use a Group even if it's not part of your design. This is because SwiftUI needs to know the exact type of what you're returning, and Group helps with that.
```swift
@State private var toggle = false
var body: some View {
    Group {
        if toggle {
            Text("It's true")
        } else {
            SFSymbol(.nosign)
        }
    }
}
```

### 13. ForEachWithIndex
```swift
public func ForEachWithIndex<Data: RandomAccessCollection, Content: View>(  _ data: Data, @ViewBuilder content: @escaping (Data.Index, Data.Element) -> Content
) -> some View where Data.Element: Identifiable, Data.Element: Hashable {
    ForEach(Array(zip(data.indices, data)), id: \.1) { index, element in
        content(index, element)
    }
}
// as a reusable View:
struct ForEachWithIndex<
    Data: RandomAccessCollection,
    Content: View
>: View where Data.Element: Identifiable, Data.Element: Hashable {
    let data: Data
    @ViewBuilder let content: (Data.Index, Data.Element) -> Content

    var body: some View {
        ForEachWithIndex(data: data) { index, element in
            content(index, element)
        }
    }
}
// usage:
ForEachWithIndex(data: viewModel.books) { index, book in
    VStack {
        BookRow(book: book)
        if index < viewModel.books.count - 1 {
            Divider()
        }
    }
}
```
### 12. Apply closure onto view

```swift
public extension View {
    @ViewBuilder
    func applyIf<T: View>(_ condition: @autoclosure () -> Bool, apply: (Self) -> T) -> some View {
        if condition() {
            apply(self)
        } else {
            self
        }
    }
}
```
### 11. Erease view type
```swift
public extension View {
    func erase() -> AnyView {
        return AnyView(self)
    }
}

```
### 10. Hide / visible:

```swift
public extension View {
    @ViewBuilder
    func hidden(_ hides: Bool) -> some View {
        switch hides {
        case true: self.hidden()
        case false: self
        }
    }
}
```
### 9. if condition transform content:
Usage:
```swift
Rectangle()
   if true == Optional(true) {
      .fill(.green)
   } else {
      .fill(.blue)
   }
```
Extension:
```swift
public extension View {
    @ViewBuilder
    func `if`<Content: View>(_ condition: Bool, transform: (Self) -> Content) -> some View {
        if condition {
            transform(self)
        } else {
            self
        }
    }
}
```

### 8. Using swiftUI preview on a real device:
To preview SwiftUI views on a physical device, follow these steps:

1. Connect your device to your development machine.
2. Open a SwiftUI view in Xcode, which will display a preview of the view on the canvas.
3. To preview the view on the connected device, toggle the "Preview On Device" button at the bottom of the canvas.
4. It may take a few moments for the preview to show up on the device.
5. Previewing on a device allows for a better sense of the look and feel of the user interface, shortens the feedback loop, and is faster than building and installing the application on the device.
6. You can use preview data to test various configurations, which can save time.
7. The preview on the device is a live preview, allowing interaction with the preview just like in the canvas.

By following these steps, you can easily preview your SwiftUI views on a physical device, which can help in faster and more effective user interface development[1].

More on how to use xcode preview here: [https://sarunw.com/posts/xcode-previews](https://sarunw.com/posts/xcode-previews) 

### 7. Adher to another views size

In SwiftUI, you can easily make two views the same size, either in height or width. You don't need any complex tools for this, just use the frame() and fixedSize() functions.

On iOS, make the maximum height or width of each view you want to size as infinite. This will make it fill all the available space. Then, apply fixedSize() to the container they are in. This tells SwiftUI that these views should only take up the space they need.

So, SwiftUI will find the smallest space the views need, and let them take up that full amount. This way, the two views will always be the same size, no matter what they contain.

For example, you can make two text views the same height even if they have different text lengths. Just use the frame() and fixedSize() functions, and both text views will be the same size.

```swift
HStack {
    Text("This is a brief text.")
        .padding()
        .frame(maxHeight: .infinity)
        .background(.red)

    Text("This is an extremely lengthy text with a significant amount of words that will certainly span multiple lines due to its length.")
        .padding()
        .frame(maxHeight: .infinity)
        .background(.green)
}
.fixedSize(horizontal: false, vertical: true)
.frame(maxHeight: 200)
```

You can use the same method to make two views have the same width.

```swift
VStack {
    Button("Sign in") { }
        .foregroundStyle(.white)
        .padding()
        .frame(maxWidth: .infinity)
        .background(.red)
        .clipShape(Capsule())

    Button("Forgot Password") { }
        .foregroundStyle(.white)
        .padding()
        .frame(maxWidth: .infinity)
        .background(.red)
        .clipShape(Capsule())
}
.fixedSize(horizontal: true, vertical: false)
```

### 6. Debug dark and light mode simultaniously:
Dark mode and light mode of any UI component, vertically stacked, ready for preview ✨
```swift
/**
 * Used to preview light-mode and dark-mode simultaniously
 */
struct PreviewContainer<Content: View>: View {
   let content: Content
   // init
   init(@ViewBuilder content: () -> Content) {
      self.content = content()
   }
   // body
   @ViewBuilder
   var body: some View {
      ZStack {
         Rectangle()
            .fill(Color.secondaryBackground)
            .ignoresSafeArea(.all)
         VStack(spacing: 0) {
            content
               .environment(\.colorScheme, .light)
            content
               .environment(\.colorScheme, .dark)
         }
      }
   }
}
// Preview
#Preview {
   PreviewContainer {
      Button(action: {
         Swift.print("on action")
      }, label: {
         Text( "Hello world")
         Spacer()
      })
         .padding(16)
         .background(Color(light: .white, dark: .black).opacity(1))
   }
}
```

### 5. Inject colorscheme in preview
Instead of writing the UI component twice to test dark / light mode. We can pass scheme in a closure as shown bellow:
```swift
// Preview
#Preview {
   let closure: (_ colorScheme: ColorScheme) -> some View = { colorScheme in
      AccessoryRow(title: "Hello world", leadingImageName: "heart")
         .padding(16)
         .background(Color(light: .white, dark: .black).opacity(1))
         .environment(\.colorScheme, colorScheme)
   }
   return ZStack {
      Rectangle()
         .fill(Color.secondaryBackground)
         .ignoresSafeArea(.all)
      VStack(spacing: 0) {
         closure(.dark)
         closure(.light)
      }
   }
}
```

### 4. Inject dismiss
- Useful if you embed NavStack in a fullScreen cover or similar 
- This can also be achived with the binding that fullScreenCover passes
- Going back via binding or dismiss has slightly different animation behaviours
```swift
struct Main: View {
    @Environment(\.dismiss) var dismiss
    var dismissHock: Environment<DismissAction> { // A hack to get _dimiss in an extension
        _dismiss
    }
    ... // Some navigation code
    Content(dismiss: dismissHock) // <- this passes the Main dismiss so that we can dismiss at that level
}
struct Content: View {
    @Environment(\.dismiss) var dismissHock // use this to dismiss at main level
    @Environment(\.dismiss) var dismiss // use this to dismiss at content level
}
```

### 3. Convenient padding extension 
You can create an extension that sets separate vertical and horizontal padding values for a view. This can be done by defining a new function that accepts both vertical and horizontal padding values.
```swift
extension View {
    func padding(vertical: CGFloat, horizontal: CGFloat) -> some View {
        self.padding(.vertical, vertical)
            .padding(.horizontal, horizontal)
    }
}
// Now you can use this new function to set separate vertical and horizontal padding values:

Text("Hello, SwiftUI!")
    .padding(vertical: 10, horizontal: 20)
```

### 2. IndexedForEach
Sometimes you need index in a for each
```swift
ForEach(Array(zip(data.indices, data)), id: \.0) { idx, item in
    content(idx, item)
}
```

### 1. selectable List

## Selectable list
SwiftuI's built in List selection functionality doesn't allow for much customization. Here is a way to do it manually:

1. Create a state that is an optional int
2. Create a list where id is the int. `List(Array(items.indecies), id: \.self)`
3. Create items: `{ i in let item = items[i] }`
3. Set the state on tap of the list item
4. Change the selection state by doing: `let isSelected = selectedIndex == i`
5. Change selected state in the item: `let row = Row(isSelected: isSelected)`

```swift
// code coming soon
```