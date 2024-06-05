My notes on passing FocusState bindings in SwiftUI<!--more--> 

> Basically: Use focused(_:) if you have a single TextField. and Use focused(_:equals:) should you have multiple TextFields.

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

## Dynamic content:
Using UUIDs for managing focus state in SwiftUI, especially with dynamically created text fields, can be challenging due to the nature of UUIDs. UUIDs are unique identifiers that are not inherently ordered or sequential, which makes them less suitable for managing focus in a predictable manner compared to using an enumeration or a simple integer index. However, if you have a specific use case where you need to use UUIDs, you can still manage focus state by associating each UUID with a specific text field and managing the focus state accordingly.

```swift
struct TextFieldModel: Identifiable {
    let id: UUID
    var text: String
}

@FocusState private var focusedFieldID: UUID?
ForEach(textFields) { field in
    TextField("Field", text: $field.text)
        .focused($focusedFieldID, equals: field.id)
}
func focusNextField() {
    guard let currentIndex = textFields.firstIndex(where: { $0.id == focusedFieldID }) else { return }
    let nextIndex = textFields.indices.contains(currentIndex + 1) ? currentIndex + 1 : 0
    focusedFieldID = textFields[nextIndex].id
}
.onAppear(perform: {
    focusedFieldID = textFields.first?.id
})
.onSubmit(focusNextField)
```


### Resources: 
- Great way to do .next call from software keyboard: https://stackoverflow.com/q/68513880
- hybrid os remove focus method: https://stackoverflow.com/a/72026504
- bouncy issue between focus: https://stackoverflow.com/questions/73755705/focusstate-changes-in-swiftui-cause-the-keyboard-to-bounce?noredirect=1&lq=1
- more elaborate solution: https://medium.com/@michaelrobertellis/how-to-programmatically-manage-focusstate-for-dynamically-created-textfields-and-texteditors-in-5516de893d1a
- focus on apear: https://m-mois.medium.com/swiftui-focus-switching-between-text-fields-e09c8944b17b#:~:text=To%20manage%20focus%20in%20SwiftUI,focus%20to%20a%20specific%20view.
- intercepting return key: https://stackoverflow.com/a/76593519/5389500
- fix for axis: .vertical issue: https://stackoverflow.com/a/74408458/5389500