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
   lazy var view: NSView = {
      let contentRect = window.contentRect(forFrameRect: window.frame)/*size of win sans titlebar*/
      let view: View = .init(frame: contentRect)
      window.contentView = view
      view.layer?.backgroundColor = NSColor.white.cgColor
      return view
   }()
   func applicationDidFinishLaunching(_ aNotification: Notification) {
      _ = view
   }
}

open class View: NSView{
   override open var isFlipped: Bool { return true }/*TopLeft orientation*/
   override public init(frame: CGRect) {
      super.init(frame: frame)
      Swift.print("hello world")
      self.wantsLayer = true/*if true then view is layer backed*/
   }
   /**
    * Boilerplate
    */
   required public init?(coder decoder: NSCoder) {
      fatalError("init(coder:) has not been implemented")
   }
}
```
