Notes on view modifiers in swiftUI<!--more-->

### Viewmodifer: 
ViewModifier is a modifier that you apply to a view or another view modifier, producing a different version of the original value.

good primer for viewmodifiers: https://sarunw.com/posts/swiftui-viewmodifier/

### Modifiers in general:

Modifiers are another part of the declarative syntax of SwiftUI that might seem puzzling at first. In imperative Swift code, we cannot append methods one after the other. SwiftUI makes it possible by using an advanced technique called method chaining.

You can also add new views directly to a preview by dragging them from the Library, which you bring up by clicking on the plus button in the Xcode toolbar.

When you want to apply the same modifiers to more than one view, you can use a Group. The modifiers will be applied to all the contained views.

In SwiftUI, layouts are centered by default. We can control the layout of stacks, but only perpendicularly to their axis. If instead, you want to move elements along the axis of a stack, you need to use one or more Spacer views.


**Frame modifier**
To make a SwiftUI view take all available width, we use .frame() modifier with maxWidth and maxHeight set to .infinity.
```swift
.frame(maxWidth: .infinity, maxHeight: .infinity)
.frame(maxWidth: .infinity, alignment: .leading) // left aligned
.frame(maxWidth: .infinity, alignment: .trailing) // right aligned
```

This is an excellent opportunity to show you all the nine alignment positions you can have.

1. .topLeading
2. .top
3. .topTrailing
4. .leading
5. .center
6. .trailing
7. .bottomLeading
8. .bottom
9. .bottomTrailing

```swift
// .modifier function
Text("Giant").modifier(BiggerModifier())
// ModifiedContent
ModifiedContent(content: Text("Giant"), modifier: BiggerModifier())
```

### View modifier:
ref: https://sarunw.com/posts/swiftui-viewmodifier/
```swift
// The protocol is: func body(content: Self.Content) -> Self.Body
struct LargerModifier: ViewModifier {
    func body(content: Content) -> some View {
        // Scale the content by a factor of 3
        return content.scaleEffect(3)
    }
}
// Apply the modifier to a text view
Text("Huge").modifier(LargerModifier())
// Alternatively, use ModifiedContent to apply the modifier
ModifiedContent(content: Text("Huge"), modifier: LargerModifier())
// Chain the modifier using an extension on View
extension View {
    func larger() -> some View {
        // Apply the LargerModifier
        self.modifier(LargerModifier())
    }
}
// Use the chained modifier on a text view
Text("Huge").larger()
```

Complex modifier example:
```swift
// Define a view modifier for content that is not suitable for children
struct NotForKids: ViewModifier {
  // Define the body of the view modifier
  func body(content: Content) -> some View {
    // Create a ZStack with the content and an overlay
    ZStack(alignment: .center, content: {
        // Give the content a layout priority of 1, blur it, and clip it
        content.layoutPriority(1)
            .blur(radius: 30).clipped()
        // Create a VStack with an image and a text view
        VStack {
            // Display an image of a slash
            Image(systemName: "slash.circle.fill").foregroundColor(.white)
            // Display a text view with the text "Not for kids"
            Text("Not for kids").font(.caption).bold().foregroundColor(.white)
        }
    })
  }
}

// Extend Image to include a function to apply the NotForKids modifier
extension Image {
    // Define a function to apply the NotForKids modifier
    func notForKids() -> some View {
        // Apply the NotForKids modifier
        self.modifier(NotForKids())
    }
}
```

Adding state and animation:

```swift
// Define a view modifier for content that is not suitable for children
struct NotForKids: ViewModifier {
    // Define a state variable for whether the content is hidden
    @State var isHidden: Bool = true
    
    // Define the body of the view modifier
    func body(content: Content) -> some View {
        // Create a ZStack with the content and an overlay
        ZStack(alignment: .center, content: {            
            // If the content is hidden, blur it and display a warning
            if isHidden {
                // Give the content a layout priority of 1, blur it, and clip it
                content.layoutPriority(1)
                    .blur(radius: 30).clipped()
                // Create a VStack with an image and a text view
                VStack {
                    // Display an image of a slash
                    Image(systemName: "slash.circle.fill").foregroundColor(.white)
                    // Display a text view with the text "Not for kids"
                    Text("Not for kids").font(.caption).bold().foregroundColor(.white)
                }
            } else {
                // If the content is not hidden, display it
                content
            }
        })
        // Add a tap gesture recognizer to toggle the hidden state
        .onTapGesture {
            // Toggle the hidden state with animation
            withAnimation { 
                self.isHidden = !self.isHidden
            }            
        }
    }
}
```

### Create custom modifiers
If you find yourself regularly repeating the same set of view modifiers – for example, making a text view have padding, be of a specific size, have fixed background and foreground colors, etc – then you should consider moving those to a custom modifier rather than repeating your code.

For example, this creates a new PrimaryLabel modifier that adds padding, a black background, white text, a large font, and some corner rounding:

```swift
// Define a view modifier for a main title
struct MainTitle: ViewModifier {
    // Define the body of the view modifier
    func body(content: Content) -> some View {
        // Modify the content with padding, a background color, a foreground color, a font, and a corner radius
        content
            .padding()
            .background(Color.black)
            .foregroundColor(Color.white)
            .font(.largeTitle)
            .cornerRadius(10)
    }
}

// You can now attach that to any view using .modifier(MainTitle()), like this:
// Define a view for the main content
struct MainContentView: View {
    // Define the body of the view
    var body: some View {
        // Create a text view with the text "Hello World" and apply the MainTitle modifier
        Text("Hello, World!")
            .modifier(MainTitle())
    }
}
```

### Resources
- conditional view modifiers: https://www.avanderlee.com/swiftui/conditional-view-modifier/
- trxtfield style: https://www.hackingwithswift.com/forums/swiftui/code-cleanup-customizing-a-textfield/5537 and https://medium.com/@ricardomongza/create-custom-textfield-styles-in-swiftui-b484b7ba31bf and https://thehappyprogrammer.com/custom-textfield-in-swiftui
- https://www.scelto.no/blog/replicating-swiftui-styles-for-custom-components
- https://movingparts.io/styling-components-in-swiftui
- https://movingparts.io/composable-styles-in-swiftui