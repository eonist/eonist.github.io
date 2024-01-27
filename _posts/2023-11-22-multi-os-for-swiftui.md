Hybrid os code in swiftUI<!--more-->

### Add customizations for an individual platform
Sometimes you have one SwiftUI view that works great on both iOS and macOS, but needs just a tiny modification – perhaps a little more padding on iOS, or slightly different styling.

For these times I recommend the following view extensions, which add `iOS()`, `macOS()`, `tvOS()`, and `watchOS()` methods for just this purpose:

```swift
// iOS
extension View {
    func iOS<Content: View>(_ modifier: (Self) -> Content) -> some View {
        #if os(iOS)
        return modifier(self)
        #else
        return self
        #endif
    }
}
// macOS
extension View {
    func macOS<Content: View>(_ modifier: (Self) -> Content) -> some View {
        #if os(macOS)
        return modifier(self)
        #else
        return self
        #endif
    }
}
// tvOS
extension View {
    func tvOS<Content: View>(_ modifier: (Self) -> Content) -> some View {
        #if os(tvOS)
        return modifier(self)
        #else
        return self
        #endif
    }
}
// watchOS
extension View {
    func watchOS<Content: View>(_ modifier: (Self) -> Content) -> some View {
        #if os(watchOS)
        return modifier(self)
        #else
        return self
        #endif
    }
}
```

Each of those follow the same pattern: they create a new method using the platform name, each of which accept a function that is able to transform the current view somehow. Inside the method there is a compiler check to see whether the current platform manages the one we expect – if so we apply the modifier function, otherwise we don’t.

Those platform checks – #if os(iOS), etc – are done at compile time, which means they will be optimized away. In fact, it’s likely the compiler will be able to optimize these methods entirely because they are so simple.

Use them like this:

```swift
Text("Hello World")
    .iOS { $0.padding(10) }
```