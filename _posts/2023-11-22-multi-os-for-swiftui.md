Hybrid os code in swiftUI<!--more-->

> Apple does not promote SwiftUI as “write once, run everywhere” but as “learn once, apply everywhere” and that is an important distinction. It seems at first glance that we can write once but only on a basic level. You are still going to need to design the appropriate UI for a platform, but it will be able to re-use components from the other versions of the same app.

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

### Not os
```swift
#if !os(macOS)
    .navigationBarTitleDisplayMode(.inline)
#endif
```

## Toggle insetgroup style

https://lucajonscher.medium.com/create-an-inset-grouped-list-in-swiftui-for-macos-20c0bcfaaa7


extension View {
    func insetGroupedStyle<V: View>(header: V) -> some View {
        #if os(iOS)
        // iOS
        #else
        // macOS
        #endif
    }
}

### Resources:
- ⭐⭐ starterkit template for macOS 3 pane app (swiftUI): https://github.com/apparata/SidebarAppTemplate
- ⭐ 3 column app starter for swiftui: https://swiftwithmajid.com/2022/10/18/mastering-navigationsplitview-in-swiftui/
- ⭐ Multi os kit: https://github.com/jordansinger/SwiftUI-Kit
- hybrid app in swiftui: https://github.com/alfianlosari/NewsAppSwiftUI
- Missing SwiftUI components: https://github.com/SwiftUIX/SwiftUIX
- ios / ipad hybrid app: (4 years old) https://github.com/alfianlosari/SwiftUI-MovieDB/tree/master
- all platform hybrid app: https://github.com/alfianlosari/SwiftUITMDbV2
- Great swiftui app for iOS: https://github.com/AlexCatch/Oak
- Great macOS app in swiftUI: https://github.com/lbrndnr/nuage-macos
- Hybrid app (Composable Architecture): (abondoned 3 years ago) https://github.com/hadiidbouk/DEV/tree/main
- Spotify clone in swiftui for iOS (complex code): https://github.com/denoni/SpotifyClone/tree/main
- Device status: https://github.com/fatbobman/MovieHunter/blob/main/MovieHunter/Share/SwiftUI/DeviceStatus.swift
- https://github.com/onmyway133/awesome-swiftui
- https://kean.blog/post/appkit-is-done
