My notes on making a status-bar-app <!--more--> in swift 4

```swift
import Cocoa

@NSApplicationMain
class AppDelegate: NSObject, NSApplicationDelegate {
   @IBOutlet weak var window: NSWindow!
   var statusBar = NSStatusBar.system
   var statusBarItem : NSStatusItem = NSStatusItem()
   var menu: NSMenu = NSMenu()
   var menuItem : NSMenuItem = NSMenuItem()

   func applicationDidFinishLaunching(_ aNotification: Notification) {
//      _ = view
      Swift.print("applicationDidFinishLaunching")
      window.close()
      //Add statusBarItem
      statusBarItem = statusBar.statusItem(withLength: -1)
      statusBarItem.menu = menu
      statusBarItem.button?.title = "Comment-styler"
      statusBarItem.button?.cell?.isHighlighted = false

      //Add menuItem to menu
      menuItem.title = "Sort tasks"
      menuItem.action = #selector(sortSelectedTasks)
      menuItem.keyEquivalent = ""
      menu.addItem(menuItem)


      let indexSelectedFilesMenuItem = NSMenuItem(title: "Index files", action: #selector(indexSelectedFiles), keyEquivalent: "")
      menu.addItem(indexSelectedFilesMenuItem)

      let seperatorMenuItem = NSMenuItem.separator()
      menu.addItem(seperatorMenuItem)

      let quitMenuItem = NSMenuItem(title: "Quit", action: #selector(quitApp), keyEquivalent: "")
      menu.addItem(quitMenuItem)
   }
   @objc func sortSelectedTasks(sender: AnyObject){
      Swift.print("sortSelectedTasks")
   }
   @objc func indexSelectedFiles(sender: AnyObject){
      Swift.print("indexSelectedFiles")
   }
   @objc func quitApp(){
      Swift.print("quitApp")
   }

}



```

- add image icon to statusbar app: [https://stackoverflow.com/questions/38461449/validatemenuitem-or-menuwillopen-not-called-for-nsmenu](https://stackoverflow.com/questions/38461449/validatemenuitem-or-menuwillopen-not-called-for-nsmenu)
- toggle darkmode[https://www.raywenderlich.com/165853/menus-popovers-menu-bar-apps-macos](https://www.raywenderlich.com/165853/menus-popovers-menu-bar-apps-macos)
