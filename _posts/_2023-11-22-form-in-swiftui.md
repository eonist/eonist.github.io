<!--more-->


### Styling
The SwiftUI framework provides us the formStyle view modifier allowing to set one of the three available styles: automatic, grouped and columns. The grouped style uses a grouped list, columns styles uses grid-based layout similar to macOS forms, automatic style uses default form style on the current platform.

```swift
struct CustomFormStyle: FormStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.content
            .border(Color.red)
    }
}

extension FormStyle where Self == CustomFormStyle {
    static var custom: CustomFormStyle { .init() }
}

struct ContentView: View {
    var body: some View {
        Form {
            // content
        }
        .formStyle(.custom)
    }
}
``` 

You can also create your own form style by creating a type conforming to the FormStyle protocol. The only requirement is the makeBody function taking an instance of FormStyleConfiguration as the parameter. You can access the content of the form by reading content property on FormStyleConfiguration type.

### Resources
- https://sarunw.com/posts/swiftui-form-styling/
- https://www.waldo.com/blog/swiftui-form-101
- https://iosapptemplates.com/blog/ios-programming/forms-swiftui
- https://www.youtube.com/watch?v=iMp61wrq3Bc
- https://www.simpleswiftguide.com/swiftui-form-tutorial-how-to-create-and-use-form-in-swiftui/
- form validation: https://github.com/hollyoops/ReactiveForm
- form that has a simple password validator. https://dev.to/kevinmaarek/forms-made-easy-with-swiftui-3b75
- has lots of info on focus state in a form: https://www.kodeco.com/31569019-focus-management-in-swiftui-getting-started?page=3
- has lots of focus tricks: https://fatbobman.medium.com/advanced-swiftui-textfield-events-focus-keyboard-c99bc9f57c91
- simple on focus: https://developermemos.com/posts/focused-modifier-swiftui
- advance focus modifer: https://stackoverflow.com/questions/72021169/implementing-a-custom-viewmodifier-that-wraps-an-existing-swiftui-viewmodifier
- style based on caret focus: https://stackoverflow.com/questions/60379010/how-to-change-swiftui-textfield-style-after-tapping-on-it