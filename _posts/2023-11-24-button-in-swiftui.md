My notes on buttons in swiftUI<!--more-->


### Button style
```swift
struct ContentView: View {
    var body: some View {
        VStack {
            Button("Plain", action: {
            }).buttonStyle(PlainButtonStyle())

            Button("Borderless", action: {
            }).buttonStyle(BorderlessButtonStyle())

            Button("Default", action: {
            }).buttonStyle(DefaultButtonStyle())
        }
    }
}
```

For macOS, we have more options, PlainButtonStyle, LinkButtonStyle, BorderedButtonStyle, and BorderlessButtonStyle.

### Customize style:
```swift
Button("Sign In", action: {})
    .buttonStyle(.plain)  
```
### Customize toggle:
```swift
Toggle("Wi-Fi", isOn: $isWiFiEnabled)
    .toggleStyle(SwitchToggleStyle())
// To
Toggle("Wi-Fi", isOn: $isWiFiEnabled)
    .toggleStyle(.switch)       
```

## Your own custom style via ButtonStyle:
You can also use ViewModifer to achive the same thing
```swift
struct CapsuleButtonStyle: ButtonStyle {    
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .padding()
            .font(.body.bold())
            .foregroundColor(.white)
            .background(
                Capsule()
                    .fill(Color.black)
            )
    }
}

Button("Sign In", action: {})
    .buttonStyle(CapsuleButtonStyle())
```

add dot syntax:

```swift
extension ButtonStyle where Self == CapsuleButtonStyle {
    static var capsule: Self {
        return .init()
    }
}
Button("Sign In", action: {})
    .buttonStyle(.capsule)
```

### Native styles:
The .automatic button style means we left the style choice in SwiftUI's hand. SwiftUI will choose the one that is appropriate for the context.

The .automatic button style resolves to the borderless button style in iOS, whereas macOS, tvOS, and watchOS use the bordered button style. The same code would result in different styles based on the context. `Button("Button Title") { }`

If you apply a button style to a container view, all the buttons in the container will use that style. `Button("Bordered Prominent Button") {}.buttonStyle(.borderedProminent)`
 
 SwiftUI got five button styles as follows.

- Automatic
- Borderless
- Plain
- Bordered
- BorderedProminent


```swift
Button("Bordered Button") {
        
    }
    .buttonStyle(.bordered)
    .tint(.pink) // You can also change the color with the tint modifier.
    .buttonBorderShape(.capsule)
    .font(.title)

Button("Bordered Prominent Button") {
        
    }
    .buttonStyle(.borderedProminent)
    .tint(.pink) // You can also change the color with the tint modifier.
    .buttonBorderShape(.roundedRectangle(radius: 8))
    .font(.title)

```

### Custom style for the button:
```swift
Button {
        
    } label: {
        Text("Bordered Button")
            .padding()
            // 1
            .foregroundColor(.pink)

            // 2
            .background(

                RoundedRectangle(
                    cornerRadius: 20,
                    style: .continuous
                )
                .fill(.pink.opacity(0.2))
            )
    }
    
    
    Button {
        
    } label: {
        Text("Bordered Prominent Button")
            .padding()
            .foregroundColor(.white)
            .background(
                RoundedRectangle(
                    cornerRadius: 20,
                    style: .continuous
                )
                .fill(.pink)
            )
    }
    .font(.title)
```
In iOS 15, the background modifier got an update that makes creating a rounded corners view easier. The new background method lets us specify the background's color and shape.

You can read more about this here.

```swift

.background(
    Color.pink.opacity(0.2),
    in: RoundedRectangle(
        cornerRadius: 20,
        style: .continuous
    )
)
```

### Padding: 

Caveat

The important thing you need to know when creating a custom button is a clickable area is the size of a button content.

In this case, it is `Text("Button Label").padding()`. So, the clickable area is as wide as the text and padding.

If you move the .padding() modifier outside the button label, your clickable area will be as small as the text.

### Transperant fill:
There is no built-in style for a rounded bordered button with a transparent fill color. You have to create this with a combination of modifiers.

We use the same technique as bordered with filled color, but instead of applying .fill to RoundedRectangle, we use .stroke instead.

```swift
Button {

} label: {
    Text("Ghost Button")
        .padding()
        .foregroundColor(.pink)
        .background(
            RoundedRectangle(
                cornerRadius: 20,
                style: .continuous
            )
            .stroke(.pink, lineWidth: 2)

        )
}
```
You can control the border color and thickness by specifying them in the .stroke modifier.


### Custom transperant fill btn:
These are perfect for customized payment buttons etc

```swift
struct GhostButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .padding()
            .foregroundStyle(.tint)
            .background(
                RoundedRectangle(
                    cornerRadius: 20,
                    style: .continuous
                )
                .stroke(.tint, lineWidth: 2)
            )
    }
}

extension ButtonStyle where Self == GhostButtonStyle {
    static var ghost: Self {
        return .init()
    }
}
```
And then use it like this.
```swift
VStack {
    Button("Ghost Button") {
        
    }
    .buttonStyle(.ghost)
    
    Button("Ghost Button") {
        
    }
    .buttonStyle(.ghost)
    .tint(.pink)
    
    Button("Ghost Button") {
        
    }
    .buttonStyle(.ghost)
    .tint(.mint)
}
.font(.title)
``` 

### How to change a button size using .controlSize modifier

You can change the size of a SwiftUI Button by using controlSize(_:).

There are four sizes to choose from:

- mini
- small
- regular (This is default size)
- large


```swift
Button("Large Button") {
            
}
.buttonStyle(.borderedProminent)
.controlSize(.large)
```

## Change size manually:

How to change a button size manually

Using controlSize is a quick and easy way to control the size of a button.

But as you see, you are giving up control to SwiftUI. If you want more control over the size you have to do it in a button label.

I usually use padding and frame to control the size of a button. But you can use anything you can think of. The label size determines the button size.

Here is an example where we control button sizes using .padding and .frame.
 

```swift

VStack {
    Button {
        
    } label: {
        Text("Custom Button")
            .padding(.horizontal, 20)

            .padding(.vertical, 8)

            .foregroundColor(.white)
            .background(.pink)
            .cornerRadius(8)
    }    
    
    Button {
        
    } label: {
        Text("Custom Button")
            .padding(.horizontal, 8)

            .frame(height: 44)

            .foregroundColor(.white)
            .background(.pink)
            .cornerRadius(8)
    }    
}
```

### Link btn:

Open URL with Link view

We can use a Link view to open a URL in Safari.

Link view is similar to a button, but instead of providing an action, we provide a destination URL that we want to open.

The example below creates a link to my Twitter profile https://twitter.com/fifa.
```swift

struct ContentView: View {
    var body: some View {
        Link("Twitter", destination: URL(string: "https://twitter.com/fifa")!)

    }
}
```
customize the link look and feel:
```swift
struct ContentView: View {
    var body: some View {
        Link("Twitter", destination: URL(string: "https://twitter.com/fifa")!)
            .font(.largeTitle)
            .foregroundColor(.pink)

    }
}
```

adding an icon and underline:

```swift
Link(destination: URL(string: "https://twitter.com/fifa")!) {
    Label("Twitter", systemImage: "link")
        .font(.largeTitle)
        .foregroundColor(.pink)
        .underline(true, color: .pink)
}
```

Adding links as a row item in a list:
```swift
struct ContentView: View {
    var body: some View {
        NavigationStack {
            List {
                Link("Website", destination: URL(string: "https://fifa.com")!)
                Link("Twitter", destination: URL(string: "https://twitter.com/fifa")!)
            }
            .foregroundColor(.black)
            .navigationTitle("About")
        }
    }
}
```

### Custom fill and stroke for a button

How to change Background color of Rounded Corner Border Button in SwiftUI

There might be several ways to do this, but I can give you two ways to do it.

- Using overlay modifier.
- Using ZStack.

### Using overlay modifier

The first method is using .overlay modifier.

The overlay modifier will put the specified view in front of the view. We use this to draw a border on top of our background.
```swift

Button {
    
} label: {
    Text("Bordered Button")
        .padding()
        .foregroundColor(.pink)
        .background(
            RoundedRectangle(
                cornerRadius: 20,
                style: .continuous
            )
            .fill(.yellow)
        )
        .overlay {

            RoundedRectangle(
                cornerRadius: 20,
                style: .continuous
            )
            .stroke(.pink, lineWidth: 2)
        }
}
``` 
We use the same RoundedRectangle on both the background and the overlay modifiers to get the same curve.

But instead of applying .fill, we apply .stroke on the one in the .overlay modifier.

Apply background with background modifier and apply border with overlay modifier.
Apply background with background modifier and apply border with overlay modifier.
Using ZStack

The second method uses the same concept as the first one. We want to draw a border over the background view.

### But instead of using overlay, we use ZStack this time.
```swift

Button {
    
} label: {
    Text("Bordered Button")
        .padding()
        .foregroundColor(.pink)
        .background(
            ZStack {

                RoundedRectangle(
                    cornerRadius: 20,
                    style: .continuous
                )
                .fill(.yellow)
                RoundedRectangle(
                    cornerRadius: 20,
                    style: .continuous
                )
                .stroke(.pink, lineWidth: 2)
            }
            
        )
}
``` 
The code is quite the same, we still need to duplicate the RoundedRectangle, but the advantage of this method is the overlay modifier is still free for other usages.


### Disablable button:

```swift
struct ContentView: View {
    // 1
    @State var username = ""


    var body: some View {
        VStack {
            // 2
            TextField("Username", text: $username)

                .textFieldStyle(.roundedBorder)
            Button("Log In") {
                
            }
            .disabled(username.isEmpty) // 3

        }
        .font(.title)
    }
}
```

### A button with icon text and value:

```swift
Button {
                
} label: {
    HStack {
        Image(systemName: "star.fill")
        Text("Star")
        Divider()
        Text("31")
    }
    .fixedSize()
}
.buttonStyle(.borderedProminent)

// 3
Button {
    // action
} label: {
    Label("Sign In", systemImage: "applelogo")
}
```

### Setting custom width:

We can try setting frame before style
```swift

 Button("Plain") {}
        // set frame here instead
        .buttonStyle(.plain)
        .frame(maxWidth: .infinity)
        .border(.pink)
        
```
or do this: 
```swift
Button {
    
} label: {
    Text("Bordered Prominent")
        .frame(maxWidth: .infinity)
}
.buttonStyle(.borderedProminent)
```

Since .frame(maxWidth: .infinity) appllied before the button style, the style will respect the specify frame.

### Resources:
- (sometimes macOS buttons can overide sizing: see for solution: https://sarunw.com/posts/swiftui-button-size/)
- tappable bg: https://sarunw.com/posts/swiftui-button-cannot-tap/
- Making a label ith rounded background using clipshape or background: https://sarunw.com/posts/swiftui-rounded-corners-view/
- Creative use of bevel effect: https://sarunw.com/posts/how-to-make-bevel-effect-in-swiftui/
- customizing a toggle with configuration and style: https://sarunw.com/posts/swiftui-toggle-customization/
- Neomorphism: https://sarunw.com/posts/how-to-create-neomorphism-design-in-swiftui/
- Themeing with ascent : https://sarunw.com/posts/tintcolor-in-swiftui/