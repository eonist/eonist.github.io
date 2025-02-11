My notes on multi column apps in swiftui<!--more-->

> From iOS 16 onwards, use NavigationSplitView to make layouts with two or three columns on iPad and Mac. You can adjust how the columns look, how wide they are, and when they are visible.

## Two-column
A two-column split view has a sidebar and detail columns:

```swift
NavigationSplitView {
  Sidebar() // Menu bar
} detail: {
  Detail() // Detail view for each of the menu item
}
```

## Three-column
```swift
NavigationSplitView {
  Sidebar() // Menu bar
} content: {
  Content() // Sub menu
} detail: {
  Detail() // Detail view for each of the sub-menu item
}
```

## Basic 2 column setup for iPad and 1 column for iOS
- Drawback is that it uses navigationbar on iphone. And buttons are provided for you without much customization options. 
- Benefit is that it works out of the box, and takes care of many things like resizing etc

```swift
import SwiftUI

fileprivate struct MasterViewer: View {
   var body: some View {
      NavigationView {
         List {
            ForEach(1..<10) { index in
               NavigationLink(destination: DetailViewer(item: index)) {
                  Text("Item \(index)")
               }
            }
         }
         .navigationBarTitle("Master")
         
         Text("Select an item")
            .font(.largeTitle)
            .foregroundColor(.gray)
      }
   }
}

fileprivate struct DetailViewer: View {
   let item: Int
   
   var body: some View {
      Text("Detail for Item \(item)")
         .font(.largeTitle)
         .navigationBarTitle("Detail")
   }
}
#Preview {
   MasterViewer()
}
```

### 2 column example using navigationsplitview:
 - has sidebar display mode button in the toolbar which toggles the sidebar visibility
 - In portrait, the split view defaults to hiding the sidebar and slides it over the detail view when toggled by the button. The split view collapses into a stack navigation for compact screens such as the iPhone and the iPad in slide over.
```swift
struct WorldView: View {
  @EnvironmentObject var store: WorldStore
  @State private var selection: Country?

  var body: some View {
    NavigationSplitView {
      List(store.countries, selection: $selection) { country in
        NavigationLink(value: country) {
          CountryCell(country: country)
        }
      }
      .navigationTitle("World")
    } detail: {
      if let country = selection {
        CountryView(country: country)
      } else {
        Text("Select a country")
      }
    }
  }
}
```

## Split View Visibility
You can programmatically control the sidebar display mode by passing a binding to a NavigationSplitViewVisibility state property to the split view:

```swift
@State private var columnVisibility =
  NavigationSplitViewVisibility.doubleColumn

var body: some View {
  NavigationSplitView(columnVisibility: $columnVisibility) { ... }
```
There are four possible values:

`automatic:` This is the default and provides a platform suitable display mode.
all: Shows all columns. Use the default leading column visibility for the current device. This is the default setting.
`doubleColumn:` Shows the content and detail columns of a three-column split view or the sidebar and detail columns of a two-column split view. Show the content column and detail area of a three-column navigation split view.
`detailOnly:` Collapses the split view to shows only the detail column. Hide the leading two columns of a three-column split view. In other words, only the detail area shows.


- Note that for a two-column split-view the all and doubleColumn modes are the same.
- You cannot remove the display mode button. 😅

### Split View Style
The split view style modifier controls whether the split view overlays the sidebar/content on the detail view or presents them side-by-side. There are three built-in styles:

automatic: This is the default and provides a context specific style.
balanced: This style reduces the size of the detail view to make room to show the leading column(s) side-by-side.
prominentDetail: This style tries to maintain the size of the detail view by showing the leading columns overlayed over the detail view.
Note: On an iPad, the automatic style gives you a balanced (side-by-side) style in landscape and prominent detail (sidebar overlay) in portrait.

For example, if I always want to show the sidebar side-by-side in both portrait and landscape I can apply the balanced style to our split view:

```swift
NavigationSplitView(columnVisibility: $columnVisibility) {
  // sidebar
} detail: {
  // detail view
}
.navigationSplitViewStyle(.balanced)

```

The sidebar is now always shown side-by-side with a reduced width detail view. Compare this with the earlier screenshot of the portrait orientation which has the sidebar in an overlay

Note that the user can still toggle the visibility of the sidebar. This is similar to the .allVisible preferred display mode of UISplitViewController.

Split View Width
Finally you can configure the preferred column widths. There are two versions of the modifier depending on whether you want a fixed or flexible width. The flexible width modifier accepts an ideal width and optional min and max values:

// Fixed column width
.navigationSplitViewColumnWidth(200)

// Flexible column width
.navigationSplitViewColumnWidth(min: 300, ideal: 400, max: 500)
I’ve been unable to get this to work with the sidebar column (FB10749141). I’m able to reduce the sidebar width, but not increase it. I’m not sure if that’s intended, the widths are preferred and the split view can ignore them if the presentation environment doesn’t allow resizing columns.

Here’s an example with a three-column split view where I’ve set a fixed width for the first two columns:

```swift
struct ContentView: View {
  @State private var columnVisibility = 
    NavigationSplitViewVisibility.all

  var body: some View {
    NavigationSplitView(columnVisibility: $columnVisibility) {
      Sidebar()
        .navigationSplitViewColumnWidth(200)
    } content: {
      Content()
        .navigationSplitViewColumnWidth(200)
    } detail: {
        Detail()
    }
    .navigationSplitViewStyle(.balanced)
  }
}

```

### NavigationSplitView

-  If a slideover is not the style you want, you can change the navigationSplitViewStyle to .balanced

```swift
enum DemoScreen: String, Codable {
    case first, second, third

    var title: String {
        rawValue.capitalized
    }
}
struct ContentView: View {

    @State
    private var selection: DemoScreen? = .first

    var body: some View {
        NavigationSplitView {
            sidebarContent
        } detail: {
            detailContent
        }
        .tint(.blue)
        .accentColor(.green)
        .navigationSplitViewStyle(.balanced)
    }
}
 
extension ContentView {

    var sidebarContent: some View {
        List {
            link(to: .first)
            link(to: .second)
            link(to: .third)
        }
    }

    func link(to page: DemoScreen) -> some View {
        NavigationLink(value: page) {
            Text(page.title)
        }
    }
}
extension ContentView {

    @ViewBuilder
    var detailContent: some View {
        if let selection = selection {
            detailContent(for: selection)
                .buttonStyle(.bordered)
        } else {
            Text("No selection")
        }
    }

    @ViewBuilder
    func detailContent(for screen: DemoScreen) -> some View {
        switch screen {
        case .first: Button("First button") {}
        case .second: Button("Second button") {}
        case .third: Button("Second button") {}
        }
    }
}
extension ContentView {
    var sidebarContent: some View {
        List(selection: $selection) {
            link(to: .first)
            link(to: .second)
            link(to: .third)
        }
        .listStyle(.sidebar)
            .listRowBackground(Color.purple)
    }
}
```

### For macOS there is also VSplitView and HSplitView
To make the dividers movable you can use this code: https://medium.com/@eastism/writer-1-how-to-use-splitview-swiftui-be5df89d3f78 
```swift

struct MyView: View {
    let title: String
    var body: some View {
        VStack{
            Spacer()
            HStack{
                Spacer()
                Text(title)
                Spacer()
            }
            Spacer()
        }
        
    }
}
struct ContentView: View {
    var body: some View {
        HSplitView{
            MyView(title: "Left Area")
            VSplitView {
                MyView(title: "Upper Right Area")
                MyView(title: "Lower Right Area")
            }
            // .frame(height: 150)
        }// .frame(width: 100)
    }
}
```

### HSplitview example:

More info on pros and cons here: https://msena.com/posts/three-column-swiftui-macos/

```swift
HSplitView {
         Rectangle() // sidebar
            .background(.red)
            .padding()
            .frame(minWidth: 200, maxWidth: 300)
         Rectangle() // main 
            .background(.blue)
            .padding()
            .frame(minWidth: 200, maxWidth: 300, alignment: .center)
            .layoutPriority(1)
         Rectangle() // detail 
            .background(.green)
            .padding()
            .frame(minWidth: 200, maxWidth: 350)
      }
      .background(Color.darkGray)
      .frame(maxWidth: .infinity, maxHeight: .infinity)
   }
```


### Portrait vs Landscape:
Ref: https://forums.developer.apple.com/forums/thread/126878
```swift
GeometryReader { geometry in
    if geometry.size.height > geometry.size.width {
        print("portrait")
    } else {
        print("landscape")
    }
}
```
or:
```swift
struct SizeClassView: View {
    @Environment(\.verticalSizeClass) var verticalSizeClass: UserInterfaceSizeClass?
    @Environment(\.horizontalSizeClass) var horizontalSizeClass: UserInterfaceSizeClass?
    
    var body: some View {
        
        if horizontalSizeClass == .compact && verticalSizeClass == .regular {
            
            Text("iPhone Portrait")
        }
        else if horizontalSizeClass == .regular && verticalSizeClass == .compact {
            
            Text("iPhone Landscape")
        }
        else if horizontalSizeClass == .regular && verticalSizeClass == .regular {
            
            Text("iPad Portrait/Landscape")
        }
    }
}
```

Another variation here: 
- Reference: https://github.com/renaudjenny/SwiftUI-with-Size-Classes
```swift
struct ContentView: View {
    @Environment(\.verticalSizeClass) var verticalSizeClass: UserInterfaceSizeClass?
    @Environment(\.horizontalSizeClass) var horizontalSizeClass: UserInterfaceSizeClass?

    var body: some View {
        Group {
            if verticalSizeClass == .regular && horizontalSizeClass == .compact {
                // iPhone Portrait or iPad 1/3 split view for Multitasking for instance
                ...
            } else if verticalSizeClass == .compact && horizontalSizeClass == .compact {
                // some "standard" iPhone Landscape (iPhone SE, X, XS, 7, 8, ...)
                ...
            } else if verticalSizeClass == .compact && horizontalSizeClass == .regular {
                // some "bigger" iPhone Landscape (iPhone Xs Max, 6s Plus, 7 Plus, 8 Plus, ...)
                ...
            } else if verticalSizeClass == .regular && horizontalSizeClass == .regular {
                // macOS or iPad without split view - no Multitasking
                ...
            }
        }
    }
}
```

### Toggle visibility:
There are four ways to display your app's views:

- In .detailOnly, only the detail view is shown, filling the whole screen.
- In .doubleColumn, both the content and detail views are shown.
- In .all, the system tries to show all three views if they exist. If there's no content view, it shows only two.
- In .automatic, the system decides the best view based on the device and its orientation.
Remember, the columnVisibility uses a binding because it updates automatically when the user interacts with your app.

In SwiftUI, the sidebar, content, and detail views are similar to the "primary", "supplementary", and "secondary" views in UIKit.

```swift
@State private var columnVisibility = NavigationSplitViewVisibility.detailOnly
var body: some View {
    NavigationSplitView(columnVisibility: $columnVisibility) {
        Text("Menu")
    } content: {
        Text("Main Content")
    } detail: {
        VStack {
            Button("Show Detail Only") {
                columnVisibility = .detailOnly
            }
            Button("Show Content and Detail") {
                columnVisibility = .doubleColumn
            }
            Button("Display All") {
                columnVisibility = .all
            }
        }
    }
}
```

### Toggle sidebar for macOS:
Ref: https://sarunw.com/posts/how-to-toggle-sidebar-in-macos/
```swift
Button("toggle sidebar") {
    withAnimation {
        toggleSidebar()
    }
}
func toggleSidebar() {
    #if os(macOS)  // only for macOS
    NSApp.keyWindow?.firstResponder?.tryToPerform(#selector(NSSplitViewController.toggleSidebar(_:)), with: nil)
    #endif
}
```

### Using navigationDestination in NavigationSplitView
```swift
import SwiftUI

struct View1: View {
   @State var isPresented = false
   @State var selectedValue: String = ""
   var body: some View {
      NavigationSplitView.init {
         List {
            Button("Go to x") {
               Swift.print("x")
               selectedValue = "1"
               isPresented = true
            }
            Button("Go to view 2") {
               Swift.print("2")
               selectedValue = "2"
               isPresented = true
            }
            .background(Color.red)
         }
         // This works anywhere it is attached etc
         .navigationDestination(isPresented: $isPresented, destination: { // we cant use item or for, because they wont work with splitview, only stackview or navigation link etc. at least icant get the to work in a simplified example
            Text("\(selectedValue)")
         })
      } detail: {
         Text("nothing selected")
      }
   }
}

struct View2: View {
   @Binding var path: NavigationPath
   var body: some View {
      Button("Go to view 3") {
         path.append("View3")
      }
      .background(Color.orange)
   }
}

struct View3: View {
   @Environment(\.dismiss) private var dismiss
   @Binding var path: NavigationPath
   var body: some View {
      Button("Pop view") {
         path.removeLast()
         // or - call `dismiss()`
         // dismiss()
      }
      .background(Color.green)
   }
}

#Preview(traits: .fixedLayout(width: 400, height: 300)) {
   View1()
      .frame(width: 400, height: 300)
}

```

### Gochas:
- Hides toggle btn `.toolbar(removing: .sidebarToggle)` (add it to the sideBar, at least for macOS)
- To avoid top inset in the content column, remember to set `.ignoresSafeArea(.all)`
- ProminentDetail does not work in macOS The first is .prominentDetail, which tells SwiftUI you want the detail view to retain its full size at all times – the sidebar and content view will slide over the detail view, rather than pushing it to one side or squeezing it smaller:

### Resources:
- Setting column min, max, ideal sizes: [https://www.hackingwithswift.com/quick-start/swiftui/how-to-customize-a-views-width-in-navigationsplitview](https://www.hackingwithswift.com/quick-start/swiftui/how-to-customize-a-views-width-in-navigationsplitview)
- Overview of how native split view looks on various platforms: [https://github.com/russell-archer/SwiftUI-SplitViewNavDemo](https://github.com/russell-archer/SwiftUI-SplitViewNavDemo)
- SwiftUI SplitView compact column control: [https://useyourloaf.com/blog/swiftui-splitview-compact-column-control/](https://useyourloaf.com/blog/swiftui-splitview-compact-column-control/)
- Working with two side-by-side views in SwiftUI: [https://www.hackingwithswift.com/books/ios-swiftui/working-with-two-side-by-side-views-in-swiftui](https://www.hackingwithswift.com/books/ios-swiftui/working-with-two-side-by-side-views-in-swiftui)
- How to implement navigation split view in SwiftUI: [https://gurjit.co/blogs/2023/how-to-implement-navigation-split-view-in-swiftui.php](https://gurjit.co/blogs/2023/how-to-implement-navigation-split-view-in-swiftui.php)
- Getting started with the SwiftUI navigation split view: [https://danielsaidi.com/blog/2022/08/08/getting-started-with-the-SwiftUI-navigation-split-view](https://danielsaidi.com/blog/2022/08/08/getting-started-with-the-SwiftUI-navigation-split-view)
- NavigationSplitView in SwiftUI: [https://www.appcoda.com/navigationsplitview-swiftui/](https://www.appcoda.com/navigationsplitview-swiftui/)
- An example where tabbar is used on iPhone and sidebar on Mac and iPad: [https://stackoverflow.com/questions/63552716/how-to-run-the-split-view-on-ipad-using-swiftui](https://stackoverflow.com/questions/63552716/how-to-run-the-split-view-on-ipad-using-swiftui)
- Adding draggable sliders for iOS / iPadOS: [https://stackoverflow.com/questions/61169930/how-to-correctly-do-up-an-adjustable-split-view-in-swiftui](https://stackoverflow.com/questions/61169930/how-to-correctly-do-up-an-adjustable-split-view-in-swiftui)
- Programmatically hide and show sidebar in SplitView: [https://nilcoalescing.com/blog/ProgrammaticallyHideAndShowSidebarInSplitView/](https://nilcoalescing.com/blog/ProgrammaticallyHideAndShowSidebarInSplitView/)
- Draggable split grid for macOS: [https://github.com/krzyzanowskim/SwiftUI.SplitView](https://github.com/krzyzanowskim/SwiftUI.SplitView)
- Draggable split view for iOS and tutorial here: [https://github.com/avdyushin/SplitView](https://github.com/avdyushin/SplitView), [https://blog.grigory.nl/posts/building-splitview-in-swiftui/](https://blog.grigory.nl/posts/building-splitview-in-swiftui/)
- Lots of info on NavigationSplitView: [https://useyourloaf.com/blog/swiftui-split-view-configuration/](https://useyourloaf.com/blog/swiftui-split-view-configuration/)
- Another tutorial on NavigationSplitView: [https://www.appcoda.com/navigationsplitview-swiftui/](https://www.appcoda.com/navigationsplitview-swiftui/)
- Manually controlling SidebarButton action: [https://nilcoalescing.com/blog/ProgrammaticallyHideAndShowSidebarInSplitView/](https://nilcoalescing.com/blog/ProgrammaticallyHideAndShowSidebarInSplitView/)
- For macOS: [https://www.kiloloco.com/articles/019-swiftui-macos-navigation-basics/](https://www.kiloloco.com/articles/019-swiftui-macos-navigation-basics/)
- Basics of NavigationSplitView: [https://medium.com/@alessandromanilii/swiftui-navigationsplitview-b5ba2df07bb4](https://medium.com/@alessandromanilii/swiftui-navigationsplitview-b5ba2df07bb4)
- Disable sidebar of NavigationSplitView programmatically: [https://stackoverflow.com/questions/77059581/disable-sidebar-of-navigationsplitview-programmatically](https://stackoverflow.com/questions/77059581/disable-sidebar-of-navigationsplitview-programmatically)
- Change visibility of details NavigationSplitView: [https://stackoverflow.com/questions/74140093/swiftui-how-to-change-visibility-of-details-navigationsplitview](https://stackoverflow.com/questions/74140093/swiftui-how-to-change-visibility-of-details-navigationsplitview)
- Hide detail column in a three-column view: [https://stackoverflow.com/questions/77686123/swiftui-navigationsplitview-hide-detail-column-in-a-three-column-view](https://stackoverflow.com/questions/77686123/swiftui-navigationsplitview-hide-detail-column-in-a-three-column-view)
- How do you dismiss the detail view in SwiftUI on an iPad split view layout: [https://stackoverflow.com/questions/70477049/how-do-you-dismiss-the-detail-view-in-swiftui-on-an-ipad-split-view-layout](https://stackoverflow.com/questions/70477049/how-do-you-dismiss-the-detail-view-in-swiftui-on-an-ipad-split-view-layout)
- How can I permanently show sidebar in a SwiftUI NavigationView or SplitNavigation: [https://stackoverflow.com/questions/75283501/how-can-i-permanently-show-sidebar-in-a-swiftui-navigationview-or-splitnavigatio](https://stackoverflow.com/questions/75283501/how-can-i-permanently-show-sidebar-in-a-swiftui-navigationview-or-splitnavigatio)
- Developer forums thread 712004: [https://forums.developer.apple.com/forums/thread/712004](https://forums.developer.apple.com/forums/thread/712004)
- Developer forums thread 708721: [https://forums.developer.apple.com/forums/thread/708721](https://forums.developer.apple.com/forums/thread/708721)
- How to use NavigationSplitView and NavigationStack in SwiftUI: [https://onmyway133.com/posts/how-to-use-navigationsplitview-and-navigationstack-in-swiftui/](https://onmyway133.com/posts/how-to-use-navigationsplitview-and-navigationstack-in-swiftui/)
- Custom SplitView (from the ground up 😅) in SwiftUI: [https://github.com/stevengharris/SplitView](https://github.com/stevengharris/SplitView)
- A pretty simple custom built resizable SplitView for macOS: [https://medium.com/@eastism/writer-1-how-to-use-splitview-swiftui-be5df89d3f78](https://medium.com/@eastism/writer-1-how-to-use-splitview-swiftui-be5df89d3f78)
- Tutorial on macOS NavigationSplitView etc.: [https://betterprogramming.pub/sidebar-and-navigationview-on-macos-in-swiftui-a8b4a074a651](https://betterprogramming.pub/sidebar-and-navigationview-on-macos-in-swiftui-a8b4a074a651)
- MinWidth and layout priority for HSplitView: [https://github.com/onmyway133/blog/issues/674](https://github.com/onmyway133/blog/issues/674)
- Lots of useful tips on NavigationSplitView: [https://www.hackingwithswift.com/quick-start/swiftui/how-to-create-a-two-column-or-three-column-layout-with-navigationsplitview](https://www.hackingwithswift.com/quick-start/swiftui/how-to-create-a-two-column-or-three-column-layout-with-navigationsplitview)
- Mastering NavigationSplitView in SwiftUI: [https://swiftwithmajid.com/2022/10/18/mastering-navigationsplitview-in-swiftui/](https://swiftwithmajid.com/2022/10/18/mastering-navigationsplitview-in-swiftui/)
- HSplitView: [https://developer.apple.com/documentation/swiftui/hsplitview](https://developer.apple.com/documentation/swiftui/hsplitview)
- Some code regarding resizable hsplitview: [https://forums.developer.apple.com/forums/thread/117710](https://forums.developer.apple.com/forums/thread/117710)