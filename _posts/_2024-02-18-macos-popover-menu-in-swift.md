My notes on popover macos menu button<!--more-->

Add buttons and dividers to make it work like in iOS

```swift
import SwiftUI

struct ContentView: View {
    @State private var showPopover: Bool = false

    var body: some View {
        Button("Show Popover") {
            self.showPopover = true
        }
        .popover(isPresented: self.$showPopover) {
            VStack {
                Text("Option 1").padding()
                Divider()
                Text("Option 2").padding()
                Divider()
                Text("Option 3").padding()
            }
        }
    }
}

```

- popover view: https://stackoverflow.com/questions/63862040/swift-ui-macos-popover-on-the-button-or-on-an-image
- taskbar popover window: https://stackoverflow.com/questions/72247783/how-to-open-a-window-from-a-menu-bar-popover-using-swiftui-on-macos
- for iOS: https://www.hackingwithswift.com/quick-start/swiftui/how-to-show-a-menu-when-a-button-is-pressed
- discuesses popover menu with image: https://www.reddit.com/r/SwiftUI/comments/zrggvw/how_to_make_a_popover_that_looks_like_this_on/
- taskbar app: https://medium.com/@acwrightdesign/creating-a-macos-menu-bar-application-using-swiftui-54572a5d5f87