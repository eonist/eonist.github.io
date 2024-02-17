My notes on passing FocusState bindings in SwiftUI<!--more--> 

In SwiftUI, if you use an underscore before 'focus', you can access the FocusState object directly. But, this is private to a View and can't be used in an extension. If you need to use FocusState in another View or in an extension, you can pass it as a Binding.

```swift
// Define an enumeration to represent the different elements that can be focused
enum FocusedElement: Hashable {
    case username
    case emailAddress
}

// Define a parent view that contains two child views
struct MainView: View {
    @FocusState var currentFocus: FocusedElement?
    
    var body: some View {
        // Pass the focus state to the child views
        SubView1(focus: _currentFocus)
        SubView2(focus: $currentFocus)
    }
}

// Define the first child view
struct SubView1: View {
    @FocusState var currentFocus: FocusElement?
    
    var body: some View {
        // Create a text field that is focused when the current focus is .username
        TextField("Username", text: .constant(""))
            .focused($currentFocus, equals: .username)
    }
}

// Define the second child view
struct SubView2: View {
    var currentFocus: FocusState<FocusedElement?>.Binding
    
    var body: some View {
        // Create a text field that is focused when the current focus is .emailAddress
        TextField("Email Address", text: .constant(""))
            .focused(currentFocus, equals: .emailAddress)
            .onAppear {
                // Set the initial focus to .emailAddress when the view appears
                currentFocus.wrappedValue = .emailAddress
            }
    }
}
```