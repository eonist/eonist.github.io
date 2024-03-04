My notes on navigation link for swift ui (NavigationStack)<!--more-->

### NavigationLink:

NavigationLink can take a binding of a boolean value **isActive** . This boolean value represents the showing state of a view.

- If the value is true, the view is pushed to a navigation stack.
- If the value is false, the view is popped or dismissed.

### NavigationStack:
In iOS 16, Apple did a big revamp on the navigation view architecture. They deprecated the NavigationView and replaced it with the NavigationStack.

https://sarunw.com/posts/how-to-pop-view-from-navigation-stack-in-swiftui/

- To push a new view to a navigation view, we add a new item to the path array.
- To pop a view, we remove an item from the path array.
- We also pass the $path to the DetailView via ` @Binding var path: [Color]` since we want the destination view to be able to pop itself. `path.removeLast()`
- To pop to root: use path = []

**Primary**
- his shows how to use path 
- another exaple of path using Color: https://sarunw.com/posts/how-to-pop-view-from-navigation-stack-in-swiftui/

```swift
struct ContentView: View {
    @State private var path: [Int] = []
    var body: some View {
        NavigationStack(path: $path) {
            Button("Start") {
                path.append(1)
            }
            .navigationDestination(for: Int.self) { int in
                DetailView(path: $path, count: int)
            }
            .navigationTitle("Home")
        }
    }
}
```

**Detail**
```swift
struct DetailView: View {
    // 5
    @Binding var path: [Int]
    let count: Int
    var body: some View {
        Button("Go deeper") {
            path.append(count + 1) // this navigates to a new vc, it trigers the parent view destination
        }
        .navigationBarTitle(count.description)
        .toolbar {
            ToolbarItem(placement: .bottomBar) {
                Button("Pop to Root") {
                    path = []
                }
            }
        }
    }
}
```

Example with one level deep nav stack: 

```swift
struct DetailView: View {
    @Binding var isShowing: Bool
    var body: some View {
        Button("Dismiss") {
            isShowing = false
        }
        .navigationTitle("Detail Title") 
    }
}
``` 

```swift
struct ContentView: View {
    @State private var isShowingDetail = false
    
    var body: some View {
        NavigationView { //  I think NavigationView has been deprecated in favour of Navigation stack
            NavigationLink("Detail", isActive: $isShowingDetail) {
                // 1
                DetailView(isShowing: $isShowingDetail)

            }
            .navigationTitle("Home")
        }
    }
}
``` 

### Multiple Navigation Destination View:

Implementing Multiple Navigation Destination Views
Often, you might need to display different destination views. For instance, if you want to display various integers, you would need to use a NavigationLink with a value that is an integer. This should be combined with a navigation destination view modifier that uses the same type. Here, I demonstrate how to display the destination text of a navigation link:

```swift
struct MainView: View {
    var body: some View {
        NavigationStack {
            List {
                NavigationLink("Proceed to detail X", value: "Display XXXX")
                NavigationLink("Proceed to Y", value: "Display YYY")
                NavigationLink("Proceed to number 2", value: 2)
            }
            .navigationDestination(for: String.self) { stringValue in
                DetailView(text: stringValue)
            }
            .navigationDestination(for: Int.self) { numericValue in
                Text("Detail with \(numericValue)")
            }
            .navigationTitle("Base view")
        }
    }
}
```


In the preceding example, you'll notice that I've utilized two navigation destination view modifiers. You might be curious as to how SwiftUI navigation determines which destination to invoke for each link. The answer lies in type mapping. When a link button with an integer type is tapped, the destination with an integer type is activated. Similarly, if a link button with a string type is tapped, the destination with a string type is used.

### Programmatic Navigation Outside of Views

All navigation must be configured within the SwiftUI view for navigation to be possible. We can trigger that navigation from other places using the steps outlined above, but what if our navigation is prompted from outside of the view hierarchy? This can be the case for Deep Linking, responding to an asynchronous event, or any number of other reasons (use your imagination). Unfortunately, there isn’t a good answer for this, but in the interest of scholarly pursuit, let’s see what we can do! Below is a potential workaround for this issue. We’ll start with our NavigationCoordinator which stores our content before we perform navigation.

**Navigation coordinator**

```swift
class NavigationManager: ObservableObject {
    /// 1 This holds the view that we want to display.
    fileprivate var targetView: AnyView = AnyView(EmptyView())
    ///2 Our binding variable for deciding when to navigate.
    @Published fileprivate var navigateNow: Bool = false
    ///3 A helper function that wraps our view and initiates navigation when assigned.
    func display<V: View>(_ view: V) {
        let wrappedView = AnyView(view)
        targetView = wrappedView
        navigateNow = true
    }
}
```

**NavigationWrapper**
> In iOS 16, Apple did a big revamp on a navigation view architecture. They deprecated the NavigationView and replaced it with the NavigationStack.

```swift
struct NavigationContainer<DisplayedView>: View where DisplayedView: View {
    /// 1 This is where we store our manager information for subsequent use during navigation.
    @EnvironmentObject var manager: NavigationManager
    /// 2 This is where we store the content that will be displayed on the view we’re navigating from. This is essentially your view body.
    private let viewContent: DisplayedView
    
    public init(@ViewBuilder viewContent: () -> DisplayedView) {
        self.viewContent = viewContent()
    }
    var body: some View {
        // We encompass our viewContent within a NavigationView, otherwise our NavigationLink will not function.
        NavigationView { // The NavigationView is used to wrap the content of your views, setting them up for subsequent navigation. 
            /// 3 Here we check to see if the manager should navigate. This will be checked whenever the environment object is updated and will trigger navigation when things have been set properly.
            if manager.navigateNow {
                NavigationLink(
                    destination: manager.targetView,
                    isActive: $manager.navigateNow,
                    label: {
                        viewContent
                    })
            } else {
                viewContent
            }
        }
        /// 4 Once we have successfully navigated away from this view, we want to set navigateNow to false to prevent the next view in the hierarchy from attempting navigation as well on load.
        .onDisappear(perform: {
            manager.navigateNow = false
        })
    }
}
```

### Navigation stack and serial linking:
three views: a home view, a detail view, and an about view. The user can navigate between these views using buttons.
```swift
import SwiftUI
// switch between the First, Second, and Third. We'll use a NavigationStack to enable navigation between these views:
struct ContentView: View {
   var body: some View {
      NavigationStack { // we can also use: NavigationView, but I think NavigationStack is prefered
         FirstView() // first view
      }
      .background(.purple)
   }
}
struct FirstView: View {
   var body: some View {
      VStack {
         Text("First")
         NavigationLink(destination: SecondView()) {
            Text("Go to Second")
         }
      }
      .background(.orange)
   }
}

struct SecondView: View {
   var body: some View {
      VStack {
         Text("Second")
         NavigationLink(destination: ThirdView()) {
            Text("Go to Third")
         }
      }
      .background(.green)
   }
}

struct ThirdView: View {
   var body: some View {
      VStack {
         Text("Third")
      }
      .background(.teal)
   }
}


#Preview {
   ContentView()
   // we can also set the navigation stack here, if we want to preview from second view etc
}

```

### Gotchas:
- Switching views with a binding and dismiss() has slightly different transition animations

### Resources:
- Some programatic hack tips (NavigationSplitView): https://bignerdranch.com/blog/the-different-forms-of-navigation-in-swiftui/
- Pop nav stack: https://sarunw.com/posts/how-to-pop-view-from-navigation-stack-in-swiftui/
- A hack that lets you bind a bool to a navlink without poping the stack: https://www.avanderlee.com/swiftui/navigationlink-programmatically-binding/
- Serialise navlink state has xcode proj: https://www.hackingwithswift.com/quick-start/swiftui/how-to-use-programmatic-navigation-in-swiftui
- Passing enviroment variable via navlink: https://stackoverflow.com/a/61711792/5389500
- Deeplinking with Navigationstack: https://www.appcoda.com/navigationstack/
- Has some custom Routing that is interesting: https://swiftwithmajid.com/2022/06/15/mastering-navigationstack-in-swiftui-navigator-pattern/
- https://onmyway133.com/posts/how-to-style-navigationlink-in-macos/
- ⭐ Lazy navigation link: https://onmyway133.com/posts/how-to-do-navigationlink-programatically-in-swiftui/