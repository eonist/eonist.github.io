Boilerplate view for swift ui<!--more-->

### Basic view with a textfield 
This is a great boilerplate for a view
```swift
import SwiftUI

struct TestView: View {
    let text: String
    var body: some View {
       Text(self.text)
    }
}
// Preview 
struct TestView_Previews: PreviewProvider {
    static var previews: some View {
       TestView(text: "Hello world")
    }
}
```

This is the new way of doing preview:
```swift
#Preview {
   return TestView()
}
```

### ForEach
```swift
struct MenuItem: Identifiable {
  let id = UUID()
  let image: UIImage
  let title: String
}

let menuItems: [MenuItem] = [
  MenuItem(image: UIImage(systemName: "person.circle")!, title: "Account"),
  MenuItem(image: UIImage(systemName: "power")!, title: "Sign Out")
]

var body: some View {     
  VStack {        
    ForEach(menuItems) { item in
      MenuView(image: item.image, title: item.title)
    }
  }
}
```

### Menu:
- MultiOS menu: https://stackoverflow.com/questions/58102800/how-to-manually-show-contextmenu-in-swiftui
- https://nemecek.be/blog/88/uimenu-comprehensive-guide
- https://sarunw.com/posts/popup-buttons-in-swiftui/
- Haptic feedback to menu: https://stackoverflow.com/questions/66717326/swiftui-menu-action-when-menu-actually-will-open
- Lots of info on menu: https://www.swiftyplace.com/blog/swiftui-menu-and-context-menu-buttons-with-dropdown-lists
- Advance and custom popover view in iOS: https://stackoverflow.com/questions/75775079/how-to-put-picker-into-context-menu-popup-in-swiftui


```swift
Menu("Actions") {
    Button("Duplicate", action: duplicate)
    Button("Rename", action: rename)
    Button("Delete…", action: delete)
    Menu("Copy") {
        Button("Copy", action: copy)
        Button("Copy Formatted", action: copyFormatted)
        Button("Copy Library Path", action: copyPath)
    }
}
If you want the button to open the menu to be an SF Symbol, like the ellipse with the three dots, you can call Menu a bit differently:

Menu {
     // Add options here...
} label: {
    Image(systemName: "ellipsis.circle")
}
```

### Text

> Combine text views
You can create new text views out of several small ones using +, which is an easy way of creating more advanced formatting. For example, this creates three text views in different colors and combines them together:

```swift
struct ContentView: View {
    var body: some View {
        Text("Colored ")
            .foregroundStyle(.red)
        +
        Text("SwifUI ")
            .foregroundStyle(.green)
        +
        Text("Text")
            .foregroundStyle(.blue)
    }
}
```

### Previewing
```swift
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
            .environment(\.sizeCategory, .accessibilityExtraExtraExtraLarge)
        ContentView()
            .preferredColorScheme(.dark)
        NavigationStack {
            ContentView()
        }
    }
}
```

### Spacer
```swift
Spacer().frame(height: 50)
```

### Returning differn view types:

```swift
struct FolderInfoView: View {
    @Binding var folder: Folder
    var isEditable: Bool

    var body: some View {
        HStack {
            Image(systemName: "folder")
            textView
        }
    }

@ViewBuilder // 👈 this is the magic attribute that allows different view types, see: https://www.swiftbysundell.com/articles/avoiding-anyview-in-swiftui/
private var textView: some View {
    if isEditable {
        TextField("Name", text: $folder.name)
    } else {
        Text(folder.name)
    }
}
}
``` 

### Container view:
> Ref: https://www.swiftbysundell.com/tips/creating-custom-swiftui-container-views/
If none of the built-in containers fit your needs, you can create custom container views. This can be done by creating a protocol that conforms to the View protocol and using it to define your custom container. Here's an example:

```swift

// Define a protocol for a generic view container
protocol ViewBoxType: View {
    associatedtype ViewContent
    init(viewContent: @escaping () -> ViewContent)
}

// Extend the protocol to initialize with a ViewBuilder
extension ViewBoxType {
    init(@ViewBuilder _ viewContent: @escaping () -> ViewContent) {
         self.init(viewContent: viewContent)
    }
}

// Define a struct that conforms to the protocol
struct ViewBox<ViewContent: View>: ViewBoxType { // you can also just use :View here
    var viewContent: () -> ViewContent
    
    var body: some View {
        viewContent()
    }
}

```
In this example, ContainerView is a protocol that defines a Content type and an initializer that takes a closure returning Content. The ContainerView protocol extends the View protocol, which means that any type that conforms to ContainerView is also a View. The ContainerView protocol also provides a convenience initializer that uses the @ViewBuilder attribute, which allows you to use multiple views in the content closure 3.

### Text

``` swift
Text("Hello, SwiftUI!")
    .font(.headline)
    // 1
    .border(Color.pink)
```

Make a text view fill its container width with frame modifier

```swift
Text("Hello, SwiftUI!")
    .background(Color.orange) // only behind text
    .font(.headline)
    .border(Color.pink)
    //.frame(maxWidth: .infinity)
    // .frame(maxWidth: .infinity, alignment: .leading) // left aligned
    // .frame(maxWidth: .infinity, alignment: .trailing) // right aligned
    // .background(Color.indigo) // behind frame
    .frame(
        maxWidth: .infinity,
        maxHeight: .infinity

    ) // To make a view taking full height, instead of setting maxWidth to .infinity, we set maxHeight to .infinity.
    .border(Color.blue)
```

### ScenePhase:
Scene Phase is a tool in SwiftUI that lets us know what's happening with our app. It can tell us if our app is in the background, active and being used, or inactive and not being used.

- Background: The app is minimized and you can't see it on the screen.
- Active: The app is open and you're using it.
- Inactive: The app is open but you're not using it, so it should stop doing things.

To use Scene Phase, we need to add a special variable to our app or view code. This will let us check what the Scene Phase is.
```swift
1	import SwiftUI
2	
3	@main
4	struct FoodApp: App {
5	    @Environment(\.scenePhase) private var scenePhase
6	
7	    var body: some Scene {
8	        WindowGroup {
9	            SignInView()
10	        }
11	        .onChange(of: scenePhase) { phase in
12	            switch phase {
13	            case .background:
14	              // Perform code when the app is in background
15	            case .active:
16	              // Perform code when the app becomes active
17	            case .inactive:
18	              // Perform code when the app becomes inactive
19	            }
20	        }
21	    }
22	}
```
### LifeCycle
A View in SwiftUI has a simple life cycle:

- Initialization: This is when the view is first created. You can set up initial values or run some code before the view is shown.
- Rendering UI: This is when SwiftUI draws the view on the screen.
- Appearing: This is when the view is fully drawn and starts to show up on the screen.
- Disappearing: This is when the view is removed from the screen.
```swift
import SwiftUI
2	
3	struct SignInView: View {
4	 
5	  init() {
6	    print("Initialization") 
7	  }
8	  
9	  var body: some View {
10	    VStack {
11	      // views
12	    }
13	      .onDisappear(perform: {
14	        print("Disappearing")
15	      })
16	      .onAppear(perform: {
17	        print("Appearing")
18	      })
19	      .renderUI()
20	  }
21	}
22	
23	extension View {
24	    func renderUI() -> Self {
25	      print("Rendering UI")
26	      
27	      return self
28	    }
29	}

The outputs are going to be:
Initialization
Rendering UI
Appearing
Disappearing
```

### Additional Event Handlers

onReceive(_:perform:)
To observe a Publisher , and perform actions when the data is omitted by the Publisher.
```swift
import SwiftUI
2	
3	struct TimerView: View {
4	  
5	  @State private var second = 0
6	  
7	  private let timer = Timer.TimerPublisher(interval: 1, runLoop: .main, mode: .common).autoconnect()
8	  
9	  var body: some View {
10	    Text("\($second) second")
11	      .onReceive(timer, perform: { _ in
12	        second += 1                          
13	      })
14	  }
15	} 
```
In this example, timer is a Publisher . Every second, the value of the timer will be published. And then the onReceive(_:perform:) will receive the value and execute the perform closure to add one to second. Finally, the drawing system will re-render TimerView with the new value of second .

**onChange(of:perform:)**
To observe a specific value, and perform actions when the value is changed.

```swift
1	import SwiftUI
2	
3	struct TimerView: View {
4	  
5	  @State private var second = 0
6	  
7	  private let timer = Timer.TimerPublisher(interval: 1, runLoop: .main, mode: .common).autoconnect()
8	  
9	  var body: some View {
10	    Text("\($second) second")
11	      .onReceive(timer, perform: { _ in
12	        second += 1                          
13	      })
14	      .onChange(of: second) { newValue in
15	        print("The value of second has been changed to \(newValue)")                       
16	      }
17	  }
18	} 
```

In the example, every time the value of second is changed, the program will execute the print statement to print the new value on the console.


### Gotchas
- Using generics instead of anyview: https://www.swiftbysundell.com/articles/avoiding-anyview-in-swiftui/
- Swiss army knif to change views on some variable change: `.onChange(of: selection) { /* do stuff */ }` on views, connected to @State var selection: Int etc
- To print something in a view chain: `let _ = DispatchQueue.main.async { print("...") }`
- "some": Holds a concrete type, Guarantees type relationship 
- "any": Holds an arbitrary concrete type, Erases type relationship

### Resources
- System icons: https://www.hackingwithswift.com/articles/237/complete-guide-to-sf-symbols
- System icon overview online: https://hotpot.ai/free-icons?s=sfSymbols
- SF symbol overview: https://github.com/andrewtavis/sf-symbols-online
- This has nuanced info on GeometryReader: https://betterprogramming.pub/geometryreader-blessing-or-curse-1ebd2d5005ec
- Nuanced info on viewthatfits: https://medium.com/the-swift-cooperative/mastering-viewthatfits-3294d74cb17b
- Safe area: https://www.fivestars.blog/articles/safe-area-insets/
- some vs any: https://github.com/onmyway133/blog/issues/888
- viewbuilder magic: https://github.com/onmyway133/blog/issues/877
- Good tutorial for onboarding: https://medium.com/@sharma17krups/onboarding-view-with-swiftui-b26096049be3
- Injecting a generic view instead of anyview: https://www.swiftbysundell.com/articles/avoiding-anyview-in-swiftui/
- some vs any: https://medium.com/@tahabebek/any-vs-some-in-swift-10a1863b6109
- Lots of good alignment and positioning tips: https://stackoverflow.com/questions/56487323/make-a-vstack-fill-the-width-of-the-screen-in-swiftui
- overlays and backgrounds: https://www.swiftbysundell.com/articles/backgrounds-and-overlays-in-swiftui/