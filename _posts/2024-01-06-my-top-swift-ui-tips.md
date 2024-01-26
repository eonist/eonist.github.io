### 6. Debug dark and light mode simultaniously:
Dark mode and light mode of any UI component, vertically stacked, ready for preview ✨
```swift
import SwiftUI
/**
 * Used to preview light-mode and dark-mode simultaniously
 */
struct PreviewContainer<Content: View>: View {
   typealias ClosureType = (_ colorScheme: ColorScheme) -> Content
   let closure: ClosureType
   // init
   init(@ViewBuilder closure: @escaping ClosureType) {
      self.closure = closure
   }
   // body
   @ViewBuilder
   var body: some View {
      ZStack {
         Rectangle()
            .fill(Color.secondaryBackground)
            .ignoresSafeArea(.all)
         VStack(spacing: 0) {
            closure(.dark)
            closure(.light)
         }
      }
   }
}
// preview
#Preview {
   PreviewContainer.init { colorScheme in
      Button.init(action: {
         Swift.print("on action")
      }, label: {
         Text( "Hello world")
      })
         .padding(16)
         .background(Color(light: .white, dark: .black).opacity(1))
         .environment(\.colorScheme, colorScheme)
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