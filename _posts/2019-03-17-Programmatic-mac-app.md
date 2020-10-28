My notes on programatic macOS app<!--more-->. This code creates a pure programatic setup w/o storyboard ~~(Uncheck use storyboard when you create the project)~~ Since Xcode 11.2 you have to tick use XIB

### The code:

```swift
import Cocoa

@NSApplicationMain
class AppDelegate: NSObject, NSApplicationDelegate {
   @IBOutlet weak var window: NSWindow!
   /**
    * Creates the view
    */
   lazy var view: NSView = createView()
   func applicationDidFinishLaunching(_ aNotification: Notification) {
      _ = view
   }
}
extension AppDelegate {
   func createView() -> NSView {
      let contentRect = window.contentRect(forFrameRect: window.frame)/*size of win sans titlebar*/
      let view: View = .init(frame: contentRect)
      window.contentView = view
      view.layer?.backgroundColor = NSColor.white.cgColor
      return view
   }
}
open class View: NSView {
   override open var isFlipped: Bool { return true }/*TopLeft orientation*/
   override public init(frame: CGRect) {
      super.init(frame: frame)
      Swift.print("hello world")
      self.wantsLayer = true/*if true then view is layer backed*/
   }
   /**
    * Boilerplate
    */
   public required init?(coder decoder: NSCoder) {
      fatalError("init(coder:) has not been implemented")
   }
}
```

## Gotchas
Sometimes you have to set the signing certificate to run locally

## Headless app:

```swift
// the bellow code must be added to AppDelegate.swift
import Cocoa

class AppDelegate: NSObject, NSApplicationDelegate {
   func applicationDidFinishLaunching(_ aNotification: Notification) {
      // Insert code here to initialize your application
      Swift.print("hello world")
   }
   func applicationWillTerminate(_ aNotification: Notification) {
      // Insert code here to tear down your application
   }
}
// the bellow code must be added to main.swift
import Cocoa

private let app = NSApplication.shared
private let delegate = AppDelegate()
app.delegate = delegate
app.run()


```
