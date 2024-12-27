My top swiftUI tips and tricks<!--more-->

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
struct StyledText: ViewBuilder {
    let text: String
    let style: TextStyle
    
    func _viewBuilder() -> some View {
        Text(text)
            .font(style.font)
            .foregroundColor(style.color)
    }
}

struct ContentView: View {
    var body: some View {
        StyledText(text: "Hello", style: TextStyle(font: .headline, color: .blue))
    }
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