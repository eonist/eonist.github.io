Notes on animating in swiftui<!--more-->


Animate changes easily
SwiftUI has two ways for us to animate changes to its view hierarchy: animation() and withAnimation(). They are used in different places, but both have the effect of smoothing out changes to the views in our app.

The animation() method is used on bindings, and it asks SwiftUI to animate any changes that result in the binding’s value being modified. For example, here’s a view that has a Toggle to show or hide a label:

struct ContentView: View {
    @State private var showingWelcome = false

    var body: some View {
        VStack {
            Toggle("Toggle label", isOn: $showingWelcome)

            if showingWelcome {
                Text("Hello World")
            }
        }
    }
}
When the toggle is changed, the text view below it will appear or disappear immediately, which isn’t a great experience. However, if we used animation() we could make the view slide in and out smoothly when the toggle is changed:

Toggle("Toggle label", isOn: $showingWelcome.animation())
You can even control the kind of animation you want, like this:

Toggle("Toggle label", isOn: $showingWelcome.animation(.spring()))
When you’re working with regular state rather than bindings, you can animate changes by wrapping them in a withAnimation() call.

For example, here’s our same view except now it shows or hide the welcome label using a button press:
```swift

struct ContentView: View {
    @State private var showingWelcome = false

    var body: some View {
        VStack {
            Button("Toggle label") {
                showingWelcome.toggle()
            }

            if showingWelcome {
                Text("Hello World")
            }
        }
    }
}
```
As with before that will cause the welcome label to appear and disappear immediately, but if we wrap our changes in withAnimation() they will be animated instead:
```swift

withAnimation {
    showingWelcome.toggle()
}
``` 
And it’s customizable in exactly the same way as animation():

```swift
withAnimation(.spring()) {
    showingWelcome.toggle()
}

```

### Resoures:
- https://sarunw.com/posts/animation-delay-and-repeatforever-in-swiftui/
- https://designcode.io/swiftui-handbook-transform-animations
https://www.appcoda.com/phaseanimator/
- https://betterprogramming.pub/animate-views-in-swiftui-5a9e7a2be21
- Animating with matchedgeometry and focus-state: https://stackoverflow.com/questions/76932907/how-do-i-pass-focusstate-through-a-viewmodifier
- Scoped animation: https://swiftwithmajid.com/2023/11/21/scoped-animations-in-swiftui/