My notes on swiftui for macOS <!--more-->

### Example app for macOS
```swift
@main
struct MyApp: App {
  var body: some Scene {
		WindowGroup {
      ContentView()
		}
  }
}
struct ContentView: View {
  @State var strings: [String] = ["A", "B", "C"]
  @State var selection: String?
  var body: some View {
	  HSplitView {
      List(selection: $selection) {
        ForEach(strings, id: \.self) { string in
          Text(string)
      	}
	    }
			.listStyle(SidebarListStyle())
	    VStack {
        Spacer()
        Text(selection ?? "<none>")
          .frame(idealWidth: 100, maxWidth: .infinity, alignment: .center)
   		  Spacer()
			}
    }
  }
}
```

### Appdelegate:
```swift
func applicationDidFinishLaunching(_ aNotification: Notification) {
    // Create the SwiftUI view that provides the window contents.
    let contentView = ContentView().touchBar(myTouchbar)

    // Create the window and set the content view.
    window = NSWindow(
        contentRect: NSRect(x: 0, y: 0, width: 480, height: 300),
        styleMask: [.titled, .closable, .miniaturizable, .resizable, .fullSizeContentView],
        backing: .buffered, defer: false)
    window.center()
    window.setFrameAutosaveName("Main Window")
    window.contentView = NSHostingView(rootView: contentView)
    window.makeKeyAndOrderFront(nil)
}
```

### Adjusting dark / light mode:
```swift
NSApp.appearance = NSAppearance(named: .darkAqua) // darkModeSelected
NSApp.appearance = NSAppearance(named: .aqua) // lightModeSelected
NSApp.appearance = nil // systemModeSelected
```

### Custom prefs view:
```swift
struct PrefsView: View {
    var body: some View {
        Text("Hello, Prefs!")
            .frame(maxWidth: .infinity, maxHeight: .infinity)
    }

    var window: NSWindow!
    init() {
        window = NSWindow.createStandardWindow(withTitle: "Preferences",
                                               width: 300,
                                               height: 100)
        window.contentView = NSHostingView(rootView: self)
        window.makeKeyAndOrderFront(nil)
    }
}
```
Open it with:
```swift
Button("Prefs") {
      let _ = PrefsView()
  }
```

A prefsview that can reopen:

```swift
struct PrefsView: View {
    @State var prefsWindowDelegate = PrefsWindowDelegate()

    var body: some View {
        Text("Hello, Prefs!")
          .frame(maxWidth: .infinity, maxHeight: .infinity)
    }

    var window: NSWindow!
    init() {
        window = NSWindow.createStandardWindow(withTitle: "Preferences",
                                               width: 300,
                                               height: 100)
        window.contentView = NSHostingView(rootView: self)
        window.delegate = prefsWindowDelegate
        prefsWindowDelegate.windowIsOpen = true
        window.makeKeyAndOrderFront(nil)
    }

    class PrefsWindowDelegate: NSObject, NSWindowDelegate {
        var windowIsOpen = false

        func windowWillClose(_ notification: Notification) {
            windowIsOpen = false
        }
    }
}
```

```swift
var prefsView: PrefsView?
Button("Prefs") {
    if let prefsView = prefsView, prefsView.prefsWindowDelegate.windowIsOpen {
        prefsView.window.makeKeyAndOrderFront(self)
    } else {
        prefsView = PrefsView()
    }
}
```

### Tips:
- Use view that fits: https://swiftwithmajid.com/2022/07/26/viewthatfits-in-swiftui/
- Use Conditional layouts in SwiftUI https://swiftwithmajid.com/2022/08/16/conditional-layouts-in-swiftui/

### Gotchas:
-  To get live preview working for macOS apps. Remember to add mac w/ appkit as a target in your app settings. iPad app as MacApp target will not be enough to be able to show macos app live preview

### Resources:
- ⭐⭐⭐ A great primer for swiftui for macOS: https://troz.net/post/2019/swiftui-for-mac-1/ and github code: https://github.com/trozware/swiftui-mac
- ⭐⭐ Programatic macos swiftui app for macOS: https://sarunw.com/posts/how-to-create-macos-app-without-storyboard/
- withouth nib: https://sarunw.com/posts/how-to-initialize-nsviewcontroller-programmatically-without-nib/
- translucent-lists-on-macos: https://www.hackingwithswift.com/quick-start/swiftui/how-to-get-translucent-lists-on-macos
- fixedSize etc: https://sarunw.com/posts/swiftui-fixedsize/
- ⭐ Lots of window tips: https://onmyway133.com/posts/how-to-manage-windowgroup-in-swiftui-for-macos/
- adding esc key support in nstextfields: https://onmyway133.com/posts/how-to-handle-escape-in-nstextfield-in-swiftui/
- How to show modal window in SwiftUI for macOS: https://onmyway133.com/posts/how-to-show-modal-window-in-swiftui-for-macos/
- not swiftui, but has a nice structure for splitview: https://github.com/KevinGutowski/SplitConfigurations
- saving a windowsize in macos: https://onmyway133.com/posts/how-to-force-set-frame-explicitly-for-nswindow/
- stepper component in swiftui for macos: https://onmyway133.com/posts/how-to-make-stepper-with-plus-and-minus-buttons-in-swiftui-for-macos/
- How to show sidebar in SwiftUI for macOS https://onmyway133.com/posts/how-to-show-sidebar-in-swiftui-for-macos/
- How to use HSplitView to define 3 panes view in SwiftUI for macOS https://onmyway133.com/posts/how-to-use-hsplitview-to-define-3-panes-view-in-swiftui-for-macos/
- Keyboar shortcuts in iPadOS and macOS: https://sarunw.com/posts/swiftui-keyboard-shortcuts/
- navsplitview that also works for macOS (a bit hard to customize): https://useyourloaf.com/blog/swiftui-split-view-configuration/ 
- Adding tabs to macOS: https://medium.com/@eastism/writer-2-swiftui-tabview-689b3b6dff40
- complex hybrid app: https://github.com/Dimillian/MovieSwiftUI
- Windowgroup: https://developer.apple.com/documentation/swiftui/windowgroup
- Adding tabs in the toolbar: https://stackoverflow.com/questions/69676612/macos-swiftui-remove-titlebar-include-windows-tabs-hsplitview-paints-above-t
- ⭐ Great tutorial on sheets in macOS (also has save panel): https://troz.net/post/2019/swiftui-for-mac-3/ and github code: https://github.com/trozware/swiftui-mac/tree/master
