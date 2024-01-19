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
// protocol is: func body(content: Self.Content) -> Self.Body
struct BiggerModifier: ViewModifier {
	func body(content: Content) -> some View {
		return content.scaleEffect(2)
	}
}
Text("Giant").modifier(BiggerModifier())
//or
ModifiedContent(content: Text("Giant"), modifier: BiggerModifier())
// chaining:
extension View {
	func bigger() -> some View {
		self.modifier(BiggerModifier())
	}
}
Text("Giant").bigger()
```

Complex modifier example:
```swift
struct NSFW: ViewModifier {
  func body(content: Content) -> some View {
    ZStack(alignment: .center, content: {
        content.layoutPriority(1)
            .blur(radius: 30).clipped()
        VStack {
            Image(systemName: "eye.slash.fill").foregroundColor(.white)
            Text("NSFW").font(.caption).bold().foregroundColor(.white)
        }
    })
  }
}
extension Image {
    func nsfw() -> some View {
        self.modifier(NSFW())
    }
}
```

Adding state and animation:

```swift
struct NSFW: ViewModifier {
    @State var isHide: Bool = true
    
    func body(content: Content) -> some View {
        ZStack(alignment: .center, content: {            
            if isHide {
                content.layoutPriority(1)
                    .blur(radius: 30).clipped()
                VStack {
                    Image(systemName: "eye.slash.fill").foregroundColor(.white)
                    Text("NSFW").font(.caption).bold().foregroundColor(.white)
                }
            } else {
                content
            }
        }).onTapGesture {
            withAnimation { 
                self.isHide = !self.isHide
            }            
        }
    }
}
```

### Create custom modifiers
If you find yourself regularly repeating the same set of view modifiers – for example, making a text view have padding, be of a specific size, have fixed background and foreground colors, etc – then you should consider moving those to a custom modifier rather than repeating your code.

For example, this creates a new PrimaryLabel modifier that adds padding, a black background, white text, a large font, and some corner rounding:

```swift
struct PrimaryLabel: ViewModifier {
    func body(content: Content) -> some View {
        content
            .padding()
            .background(.black)
            .foregroundStyle(.white)
            .font(.largeTitle)
            .cornerRadius(10)
    }
}
// You can now attach that to any view using .modifier(PrimaryLabel()), like this:
struct ContentView: View {
    var body: some View {
        Text("Hello World")
            .modifier(PrimaryLabel())
    }
}
```


### Resources
- conditional view modifiers: https://www.avanderlee.com/swiftui/conditional-view-modifier/
- trxtfield style: https://www.hackingwithswift.com/forums/swiftui/code-cleanup-customizing-a-textfield/5537 and https://medium.com/@ricardomongza/create-custom-textfield-styles-in-swiftui-b484b7ba31bf and https://thehappyprogrammer.com/custom-textfield-in-swiftui