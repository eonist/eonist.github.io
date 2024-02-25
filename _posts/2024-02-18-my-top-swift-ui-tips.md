My top swiftUI tips and tricks<!--more-->

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