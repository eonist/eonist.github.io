My notes on navigationbar for swift-ui<!--more-->

### Basic:
```swift
struct ContentView: View {
    var body: some View {
        NavigationStack {
            Text("SwiftUI")
                .navigationTitle("Welcome")
                .toolbar {
                    Button("About") {
                        print("About tapped!")
                    }

                    Button("Help") {
                        print("Help tapped!")
                    }
                }
        }
    }
}
```
### With toolbar
```swift
struct ContentView: View {
    var body: some View {
        NavigationStack {
            Text("SwiftUI")
                .navigationTitle("Welcome")
                .toolbar {
                    ToolbarItem(placement: .navigationBarLeading) {
                        Button("Help") {
                            print("Help tapped!")
                        }
                    }
                }
        }
    }
}
```
### Using ToolbarItemGroup
```swift
struct ContentView: View {
    var body: some View {
        NavigationStack {
            Text("SwiftUI")
                .navigationTitle("Welcome")
                .toolbar {
                    ToolbarItemGroup(placement: .primaryAction) {
                        Button("About") {
                            print("About tapped!")
                        }

                        Button("Help") {
                            print("Help tapped!")
                        }
                    }
                }
        }
    }
}
```

```swift
NavigationStack {
    Text("SwiftUI")
        .navigationTitle("Welcome")
        .toolbar {
            ToolbarItemGroup(placement: .primaryAction) {
                Button("About") {
                    print("About tapped!")
                }

                Button("Help") {
                    print("Help tapped!")
                }
            }

            ToolbarItemGroup(placement: .secondaryAction) {
                Button("Settings") {
                    print("Credits tapped")
                }

                Button("Email Me") {
                    print("Email tapped")
                }
            }
        }
}
```
### Multiple toolbaritem's
```swift
struct ContentView: View {
    var body: some View {
        NavigationView {
            Text("Content")
                .navigationTitle("Navigation Title")
                .toolbar {
                    // 1
                    ToolbarItem(placement: .navigationBarTrailing) {

                        Button("Add") {}
                    }
                    // 2
                    ToolbarItem(placement: .navigationBarLeading) {

                        Button("Cancel") {}
                    }
                }
        }
    }
}
```
### Combining toolbaritem and toolbaritemgroup:
```swift
struct ContentView: View {
    var body: some View {
        NavigationView {
            Text("Content")
                .navigationTitle("Navigation Title")
                .toolbar {
                    ToolbarItem(placement: .navigationBarTrailing) {

                        Button("Add 1") {}
                    }
                    
                    ToolbarItemGroup(placement: .navigationBarLeading) {
                        Button("Cancel") {}
                    }
                    
                    ToolbarItem(placement: .navigationBarTrailing) {

                        Button("Add 2") {}
                    }
                }
        }
    }
}
```

```swift
struct ContentView: View {
    var body: some View {
        NavigationView {
            Text("Content")
                .navigationTitle("Navigation Title")
                .toolbar {
                    ToolbarItemGroup(placement: .navigationBarLeading) {
                        Button("Cancel") {}
                    }
                    ToolbarItemGroup(placement: .navigationBarTrailing) {
                        Button("Add 1") {}
                        Button("Add 2") {}
                    }
                }
        }
    }
}
```

### Add + icon as a button:
This can be added to the toolbar for instance:
```swift
Button {
    print ("Add Item") // Perform an action 
} label: {
    Image (systemName:"plus")
}
```

### To hide default back btn:

```swift
.navigationBarBackButtonHidden(true)
```

### Small and large title in navbar: 
When you add a title to a navigation bar, you’ll notice it uses a large font for that title. You can get a small font by adding another modifier:

```swift
.navigationBarTitleDisplayMode(.inline)
``` 

### Resources:
- Hiding back btn: https://stackoverflow.com/questions/56571349/custom-back-button-for-navigationviews-navigation-bar-in-swiftui
- Custom back button: https://onmyway133.com/posts/how-to-make-custom-navigation-bar-in-swiftui/