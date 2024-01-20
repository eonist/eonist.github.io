Boilerplate view for swift ui<!--more-->

### Basic view with a textfield 
This is a great boilerplate for a view
```swift
import SwiftUI

struct TestView: View {
    let text: String
    var body: some View {
       Text(self.text)
    }
}
// Preview 
struct TestView_Previews: PreviewProvider {
    static var previews: some View {
       TestView(text: "Hello world")
    }
}
```

This is the new way of doing preview:
```swift
#Preview {
   return TestView()
}
```

### ForEach
```swift
struct MenuItem: Identifiable {
  let id = UUID()
  let image: UIImage
  let title: String
}

let menuItems: [MenuItem] = [
  MenuItem(image: UIImage(systemName: "person.circle")!, title: "Account"),
  MenuItem(image: UIImage(systemName: "power")!, title: "Sign Out")
]

var body: some View {     
  VStack {        
    ForEach(menuItems) { item in
      MenuView(image: item.image, title: item.title)
    }
  }
}
```

### Menu:
- MultiOS menu: https://stackoverflow.com/questions/58102800/how-to-manually-show-contextmenu-in-swiftui
- https://nemecek.be/blog/88/uimenu-comprehensive-guide
- https://sarunw.com/posts/popup-buttons-in-swiftui/
- Haptic feedback to menu: https://stackoverflow.com/questions/66717326/swiftui-menu-action-when-menu-actually-will-open
- Lots of info on menu: https://www.swiftyplace.com/blog/swiftui-menu-and-context-menu-buttons-with-dropdown-lists
- Advance and custom popover view in iOS: https://stackoverflow.com/questions/75775079/how-to-put-picker-into-context-menu-popup-in-swiftui


```swift
Menu("Actions") {
    Button("Duplicate", action: duplicate)
    Button("Rename", action: rename)
    Button("Delete…", action: delete)
    Menu("Copy") {
        Button("Copy", action: copy)
        Button("Copy Formatted", action: copyFormatted)
        Button("Copy Library Path", action: copyPath)
    }
}
If you want the button to open the menu to be an SF Symbol, like the ellipse with the three dots, you can call Menu a bit differently:

Menu {
     // Add options here...
} label: {
    Image(systemName: "ellipsis.circle")
}
```

### Text

> Combine text views
You can create new text views out of several small ones using +, which is an easy way of creating more advanced formatting. For example, this creates three text views in different colors and combines them together:

```swift
struct ContentView: View {
    var body: some View {
        Text("Colored ")
            .foregroundStyle(.red)
        +
        Text("SwifUI ")
            .foregroundStyle(.green)
        +
        Text("Text")
            .foregroundStyle(.blue)
    }
}
```

### Previewing
```swift
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
            .environment(\.sizeCategory, .accessibilityExtraExtraExtraLarge)
        ContentView()
            .preferredColorScheme(.dark)
        NavigationStack {
            ContentView()
        }
    }
}
```

### Spacer
```swift
Spacer().frame(height: 50)
```

### Returning differn view types:

```swift
struct FolderInfoView: View {
    @Binding var folder: Folder
    var isEditable: Bool

    var body: some View {
        HStack {
            Image(systemName: "folder")
            textView
        }
    }

@ViewBuilder // 👈 this is the magic attribute that allows different view types, see: https://www.swiftbysundell.com/articles/avoiding-anyview-in-swiftui/
private var textView: some View {
    if isEditable {
        TextField("Name", text: $folder.name)
    } else {
        Text(folder.name)
    }
}
}
``` 

### Container view:
> Ref: https://www.swiftbysundell.com/tips/creating-custom-swiftui-container-views/
If none of the built-in containers fit your needs, you can create custom container views. This can be done by creating a protocol that conforms to the View protocol and using it to define your custom container. Here's an example:

```swift

// Define a protocol for a generic view container
protocol ViewBoxType: View {
    associatedtype ViewContent
    init(viewContent: @escaping () -> ViewContent)
}

// Extend the protocol to initialize with a ViewBuilder
extension ViewBoxType {
    init(@ViewBuilder _ viewContent: @escaping () -> ViewContent) {
         self.init(viewContent: viewContent)
    }
}

// Define a struct that conforms to the protocol
struct ViewBox<ViewContent: View>: ViewBoxType { // you can also just use :View here
    var viewContent: () -> ViewContent
    
    var body: some View {
        viewContent()
    }
}

```
In this example, ContainerView is a protocol that defines a Content type and an initializer that takes a closure returning Content. The ContainerView protocol extends the View protocol, which means that any type that conforms to ContainerView is also a View. The ContainerView protocol also provides a convenience initializer that uses the @ViewBuilder attribute, which allows you to use multiple views in the content closure 3.

### Gotchas
- Using generics instead of anyview: https://www.swiftbysundell.com/articles/avoiding-anyview-in-swiftui/
- Swiss army knif to change views on some variable change: `.onChange(of: selection) { /* do stuff */ }` on views, connected to @State var selection: Int etc
- To print something in a view chain: `let _ = DispatchQueue.main.async { print("...") }`
- "some": Holds a concrete type, Guarantees type relationship 
- "any": Holds an arbitrary concrete type, Erases type relationship

### Resources
- System icons: https://www.hackingwithswift.com/articles/237/complete-guide-to-sf-symbols
- System icon overview online: https://hotpot.ai/free-icons?s=sfSymbols
- SF symbol overview: https://github.com/andrewtavis/sf-symbols-online
- This has nuanced info on GeometryReader: https://betterprogramming.pub/geometryreader-blessing-or-curse-1ebd2d5005ec
- Nuanced info on viewthatfits: https://medium.com/the-swift-cooperative/mastering-viewthatfits-3294d74cb17b
- Safe area: https://www.fivestars.blog/articles/safe-area-insets/
- some vs any: https://github.com/onmyway133/blog/issues/888
- viewbuilder magic: https://github.com/onmyway133/blog/issues/877
- Good tutorial for onboarding: https://medium.com/@sharma17krups/onboarding-view-with-swiftui-b26096049be3
- Injecting a generic view instead of anyview: https://www.swiftbysundell.com/articles/avoiding-anyview-in-swiftui/
- some vs any: https://medium.com/@tahabebek/any-vs-some-in-swift-10a1863b6109
- Lots of good alignment and positioning tips: https://stackoverflow.com/questions/56487323/make-a-vstack-fill-the-width-of-the-screen-in-swiftui
- overlays and backgrounds: https://www.swiftbysundell.com/articles/backgrounds-and-overlays-in-swiftui/