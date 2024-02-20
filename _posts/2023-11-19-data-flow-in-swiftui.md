My notes on data-flow in swiftui<!--more-->

The key difference between `@StateObject` and **@ObservedObject** is that **@StateObject** is designed to be used within a single view, while **@ObservedObject** is meant to share the same object instance across multiple views.

> A reactive interface is one where you can bind a UI element to a piece of data and then when the data changes, the user interface updates automatically. You can even bind both ways so that the user interface updates the data without you having to do a thing. Imagine how much boiler-plate code that can eliminate?

### @State and @Binding 
- A binding is a (2-way) connection between a piece of data and some UI
- You can't cast abinding type to another, but you can rebind with `.wrappedValue`
- **@State** is designed to be used only inside view, so follow this rule easily take a habit to give state property a private, always.

```swift
struct PlayerView: View {
  let episode: Episode
  @State private var isPlaying: Bool = false
  var body: some View {
    VStack {
      Text(episode.title).foregroundColor(isPlaying ? .white: .gray)
      Text(episode.showTitle).font(.caption).foregroundColor(.gray)
      PlayButton(isPlaying: $isPlaying)
    }
  }
}
struct PlayButton: View {
  @Binding var isPlaying: Bool
  var body: some View {
   Button(action: {
    self.isPlaying.toggle()
   }) {
    Image(systemName: isPlaying ? "pause.circle": "play.circle")
   }
  }
}
```

### @ObservedObject + ObservableObject + @StateObject
Outer to inner data flow relationship

> An advance stateobject tutorial https://medium.com/expedia-group-tech/observableobject-published-and-protocols-with-swiftui-uikit-and-cuckoo-cce69a47f08a

```swift
class UserProgress: ObservableObject {
    @Published var score = 0
}

struct InnerView: View {
    @ObservedObject var progress: UserProgress

    var body: some View {
        Button("Increase Score") {
            progress.score += 1
        }
    }
}

struct ContentView: View {
    @StateObject var progress = UserProgress()

    var body: some View {
        VStack {
            Text("Your score is \(progress.score)")
            InnerView(progress: progress)
        }
    }
}
```

```swift
class UserSettings: ObservableObject {
  @Published var username = "Anonymous"
}

struct ContentView: View {
  @ObservedObject var settings = UserSettings()

  var body: some View {
    VStack {
      Text("Hello, \(settings.username)!")
      Button("Change Username") {
        settings.username = "John Doe"
      }
    }
  }
}
```


### EnvironmentObject:
The `@EnvironmentObject` property wrapper allows you to share data across multiple views in a SwiftUI hierarchy. It eliminates the need for manual passing of data through view hierarchies and provides a global approach to data sharing. It allows you to inject an object at the top level of the view hierarchy and access it from any view below that level.

SwiftUI gives us both `@Environment` and `@EnvironmentObject` property wrappers, but they are subtly different: whereas @EnvironmentObject allows us to inject arbitrary values into the environment, @Environment is specifically there to work with SwiftUI’s own pre-defined keys.

For example, @Environment is great for reading out things like a Core Data managed object context, whether the device is in dark mode or light mode, what size class your view is being rendered with, and more – fixed properties that come from the system. In code, it looks like this:

```swift
@Environment(\.horizontalSizeClass) var horizontalSizeClass
@Environment(\.managedObjectContext) var managedObjectContext
```
On the other hand, @EnvironmentObject is designed for arbitrary objects to be read from the environment, like this:

@EnvironmentObject var order: Order
That difference might sound small, but it’s important because of the way @EnvironmentObject is implemented. When we say that order is of type Order SwiftUI will look through its environment to find an object of that type and attach it to the order property. However, when using @Environment the same behavior isn’t possible, because many things might share the same data type.

For example:

```swift
@Environment(\.accessibilityReduceMotion) var reduceMotion
@Environment(\.accessibilityReduceTransparency) var reduceTransparency
@Environment(\.accessibilityEnabled) var accessibilityEnabled
```
All three of those environment keys return a Boolean, so without specifying exactly which key we mean it would be impossible to read them correctly.


### SceneStorage
If you want to save unique data for each of your screens, you should use SwiftUI’s @SceneStorage property wrapper. This works a bit like @AppStorage in that you provide it with a name to save things plus a default value, but rather than working with UserDefaults it instead gets used for state restoration – and it even works great with the kinds of complex multi-scene set ups we see so often in iPadOS.

For example, if you have a text editor and want to store what the user was typing, you should use this kind of code:

```swift
struct ContentView: View {
    @SceneStorage("text") var text = ""

    var body: some View {
        NavigationStack {
            TextEditor(text: $text)
        }
    }
}
```

Because that uses @SceneStorage, SwiftUI will automatically make sure that each scene instance has its own copy of the text – if you run the app side by side both will save and restore their data correctly.

Now, before you use @SceneStorage there are some important warnings from Apple:

Don’t save lots of data; save just what you need for state restoration.
Never store sensitive data in scene storage, because it isn’t secure.
If the user goes to the app switcher and destroys your app, the scene storage is also destroyed.


### App State:
```swift
class AppState: ObservableObject {
    @Published var value: Int = 0
}

struct CustomView: View {
    @EnvironmentObject var appState: AppState
    
    var body: some View {
        Text("Value: \(appState.value)")
    }
}
```

**More complex examle:**

```swift
@main
struct MyApplication: App {
    // Create an instance of UserData, which will be used throughout the app.
    let userData = UserData()

    var body: some Scene {
        WindowGroup {
            // Set the environmentObject for MainView, making the userData instance available to all its child views.
            MainView()
                .environmentObject(userData)
        }
    }
}

class UserData: ObservableObject {
    // Observable properties, changes to which will trigger view updates.
    @Published var name: String = "John Doe"
    @Published var years: Int = 30

    // Function to increment the years property.
    func addYear() {
        years += 1
    }
}

struct MainView: View {
    // Access the userData instance from the environment.
    @EnvironmentObject var userData: UserData

    var body: some View {
        VStack {
            // Display the years property from userData.
            Text("Years: \(userData.years)")
            // Button to call the addYear() method of userData when tapped.
            Button {
                userData.addYear()
            } label: {
                Text("Click to add year")
                    .padding()
            }

            // Display the UserView, which will also have access to userData.
            UserView()
        }
        .font(.title)
    }
}

struct UserView: View {
    // Access the userData instance from the environment.
    @EnvironmentObject var userData: UserData

    var body: some View {
        VStack {
            // Display the years property from userData.
            Text("Years: \(userData.years)")
            // Button to call the addYear() method of userData when tapped.
            Button {
                userData.addYear()
            } label: {
                Text("Add Year")
                    .padding()
            }
        }
        .padding()
    }
}
```

### Class vs struct:
When using SwiftUI, developers tend to use a mix of structs and classes for their models depending on the use case. Both approaches have their advantages and disadvantages.

Structs and Bindings:

Structs are value types, which means they are passed by value (copied) when used in functions or assigned to variables. They are lightweight and efficient, especially for small models. Using Binding with structs allows you to create two-way bindings between the UI and the model, enabling updates in the UI to propagate back to the model.

Classes and ObservableObjects:

Classes are reference types, which means they are passed by reference (memory address) when used in functions or assigned to variables. They allow for more complex behaviors, including inheritance. Using ObservableObject and @Published with classes allows you to create data models that automatically notify their observers (e.g., SwiftUI views) when their properties change, which in turn triggers a UI update. As you've found, using ObservableObjects might be a better fit for your particular case due to the issue you encountered with the new Swift Navigation API. Converting your models to classes and making them conform to the ObservableObject protocol would allow you to use @ObservedObject or @StateObject for passing the models around, which can help manage state and avoid potential issues with bindings.

While it might require some refactoring, using classes and ObservableObjects can provide more flexibility and stability for your app. Ultimately, the choice between struct/Binding and class/ObservableObject depends on your app's requirements and how you need to manage data and state changes.

### AppStorage:
```swift
@AppStorage("emailAddress") var emailAddress: String = "sample@email.com"
```


### Updating a View:
Here are all types of Data that will trigger a SwiftUI View update.

```
@State // "parent or binding"
@Binding // "child of state"
@StateObject // "parent of observedObject"
@ObservedObject // "child of stateobject"
@EnvironmentObject
@Environment 
@FetchRequest
@AppStorage
@SceneStorage
@Published inside ObservableObject
``` 

More here: https://sarunw.com/posts/how-to-reload-view-in-swiftui/



### @State
- @State wrapper causes a view to re-render whenever it is changed
- @State is designed for local/private changes inside a view, such as a button highlight or any internal view state.
- Think of state as the parent closure handler code, and binding as the closure in the child
- A @State variable means to use internally within a view. Apple suggested we always declare state variables as private.
- Exposing a state variable as we did might cause confusion to a newcomer or even your future self since the state variable can only initialize once.


```swift
struct CounterView: View {
    @State private var count = 0
    var body: some View {
        VStack {
            Text("Counter: \(count)")
            Button("+1") {
                count += 1
            }
        }
    }
}
// We can use it like this.

struct ContentView: View {
    var body: some View {
        CounterView()

            .font(.title)
    }
}
```
**initialize @State variable in an initializer**
Setting a @State variable like this will only work for the very First time. After the @State variable is create and initialize, SwiftUI will make sure it persisted through the lifetime of the view.


```swift
// A view that displays a tally and a button to increment it
struct TallyView: View {
    // State variable to keep track of the tally
    @State private var tally: Int

    // Initializer that sets the initial tally
    init(tally: Int) {
        // This is what the compiler synthesizes behind the scene.
        // One case that it might not yield the result you expected is when a @State variable is an optional value.
        self.tally = tally
        _tally = State(initialValue: tally)
    }

    var body: some View {
        VStack {
            // Display the current tally
            Text("Tally: \(tally)")
            // Button to increment the tally
            Button("+1") {
                tally += 1
            }
        }
    }
}

// Then we can use it like this.
struct MainView: View {
    var body: some View {
        // Initialize a TallyView with a starting tally of 5
        TallyView(tally: 5)
            .font(.title)
    }
}
```
### StateObject:
> StateObject is like a reactive model for a view
⭐ ref: https://sarunw.com/posts/manually-initialize-stateobject/
```swift
// Define a class for the user profile view model
class UserProfileViewModel: ObservableObject {
    // Publish the welcome message so the view updates when it changes
    @Published var welcomeMessage: String
    // Initialize the view model with a user name
    init(userName: String) {
        welcomeMessage = "Welcome, \(userName)!"
    }
}

// Define a view for the user profile
struct UserProfileView: View {
    // Create a state object for the view model
    @StateObject private var viewModel: UserProfileViewModel
    // Initialize the view with a user name
    init(userName: String) {
        // Initialize the view model with the user name
        _viewModel = StateObject(wrappedValue: UserProfileViewModel(userName: userName))
    }
    // Define the body of the view
    var body: some View {
        // Display the welcome message from the view model
        Text("Message: \(viewModel.welcomeMessage)")
    }
}
```
### @Binding
- We use @Binding property wrapper to define an explicit dependency to a source of truth without owning it. With @Binding, you can read and write to any data that bind to your @Binding variable. The framework will make sure its always in sync.
- @Binding is a suitable tool for any view mean to be reusable, since the view doesn't care where that data comes from; it just knows how to render according to that data. Most standard SwiftUI components using this, e.g. Toggle, TextField, and Slider.
 

```swift
// Define a public struct for a switch view
public struct Switch<Label>: View {
    // Initialize the switch with a binding to a boolean and a label
    public init(
        isActive: Binding<Bool>,
        label: () -> Label
    )
}

// Define a public struct for a text field view
public struct InputField: View {
    // Initialize the text field with a binding to a string
    init(
        _ text: Binding<String>
    )
}

// The $ sign is used to get a Binding from @State, thanks to the property wrapper.

// Define a state variable for the switch status
@State var switchStatus: Bool = false
   
// Define the body of the view
var body: some View {
  // Create a switch with a label and bind it to the switchStatus variable
  Switch("Switch", isActive: $switchStatus)     
}
```
More on bindings: https://sarunw.com/posts/binding-initialization/


### Publisher:
- The Publisher is a tool to bridge between the old world and the new world.
- Several Foundation types expose their functionality through publishers, including Timer, NotificationCenter, and URLSession. Combine also provides a built-in publisher for any property that’s compliant with Key-Value Observing.

```swift
// Define a struct for a view that listens for keyboard notifications
struct KeyboardListenerView: View {
    // Define a state variable for the text field content
    @State var textFieldContent: String = "Initial Text"
   
    // Define the body of the view
    var body: some View {
        // Create a text field that updates textFieldContent
        TextField("Keyboard Listener", text: $textFieldContent)
        // Listen for the keyboardWillShowNotification
        .onReceive(NotificationCenter.default.publisher(for: UIResponder.keyboardWillShowNotification)) { (output) in
            // When the notification is received, update textFieldContent
            self.textFieldContent = "Keyboard is about to appear"
        }
    }
}
```

### @Environment
 One of a mechanism that SwiftUI use to pass data from one view to another. Like a global variable. But reactive. Great for glbal values like colorscheme or darkmode, locale, calender etc. things that can change from a global perspective and needs to propegate down to components.

 define your own custom keys/values and that is what we are going to learn in this article. To define Environment values you need two things.

EnvironmentKey
EnvironmentValues

```swift
// Define a private struct for a key to access a secure environment value
private struct SecureKey: EnvironmentKey {
    // Set the default value for the key
    static let defaultValue: Bool = false
}

// Extend EnvironmentValues to include the new key/value pair
extension EnvironmentValues {
    // Define a variable to get and set the secure environment value
    var isSecure: Bool {
        get { self[SecureKey.self] }
        set { self[SecureKey.self] = newValue }
    }
}

// Define a view that uses the secure environment value
struct MainView: View {
    // Access the secure environment value
    @Environment(\.isSecure) var isSecure
    
    var body: some View {
        // Display a text view and set the secure environment value
        Text("Hello")
            .environment(\.isSecure, true)
    }
}

// Extend View to include a modifier for the secure environment value
extension View {
    // Define a function to set the secure environment value
    func isSecure(_ value: Bool) -> some View {
        environment(\.isSecure, value)
    }
}

// Use the new modifier to set the secure environment value
Text("Hello")
    .isSecure(true)
``` 

### ObservableObject protocol

ObjservableObject is a protocol that SwiftUI provided to expose your object to the SwiftUI as a source of truth. Think of it as a tool to equip your object with a goodness @State get, but this time you manage the persistence storage yourself.

You use this when you want a reference type source of truth, which is great for the model you already have.

```swift
class Foo: ObservableObject {
  @Published var show = false
}
```
You conform your class to ObservableObject protocol and put @Published property wrapper on a variable that you want to keep track of the change. That's all you need to do to make your existing class working in SwiftUI.

Behind the scene, ObservableObject also use Publisher to emit change to interested parties. Like mentioned before, "Publisher is a single abstraction for representing external changes to SwiftUI".


```Swift 
// The above example translate to something like this.
class Bar: ObservableObject {
  let objectWillChange = PassthroughSubject<Void, Never>()
   
  var show = false {
    willSet {
      objectWillChange.send()
    }
  }
}
```
@ObservedObject

Just like we declare a dependency on @State with @Binding, we use @ObservedObject to declare a dependency on ObservableObject.

Usage

You use it just like @Binding. The only difference is you use @ObservedObject with an ObservableObject. Which is suitable for the view that depends on a model object.


```swift
struct MyView: View {
	@ObservedObject var model: MyModelObject

}

MyView(model: modelInstance)
```

You can also get Binding from an individual property of ObservableObject with the following syntax.

`$model.property`

Which you can use with @Binding.

```swift
var body: some View {
  Toggle("Toggle", isOn: $model.booleanProperty)     
}
```

### @EnvironmentObject

@EnvironmentObject is just another way of declaring a dependency on ObservableObject, but this time indirectly. With @ObservedObject you have to pass your data around hop by hop, which might feel cumbersome in some cases where that model might need to be consumed in many places. With@EnvironmentObject, you can inject that data from any ancestor view.

The downside of this is it won't be obvious on what ObservableObject needs to be set. To figure it out, you might need to go through the view hierarchy to see which object is needed for @EnvironmentObject. Failing to do this might cause run time error where @EnvironmentObject is not correctly set.

```swift
contentView.environmentObject(foo)
```

The entire hierarchy of contentView can access foo data by declare @EnvironmentObject

```swift
struct SomeViewDownTheHeirarchy: View {
    @EnvironmentObject var foo: Foo
    ...
}
```

### @Environment

SwiftUI also provided many environment values[4] you use, e.g., colorScheme, locale, sizeCategory. There might be a time when you need to adjust your view based on these values. When you want to do that, you can use @Environment property wrapper to reads a value from the view’s environment

```swift
struct ContentView: View {
  @Environment(\.colorScheme) var colorScheme
	...
}
``` 

You can override this environment value by injecting it to any view just like @EnvironmentObject, and the entire view hierarchy will get that effect.

```swift
// An example to force view to be dark mode
contentView.environment(\.colorScheme, .dark)
```

### Binding var:
binds a var inside taskstore
```swift
// Define a struct for a job view
struct JobView : View {
    // Define a variable for the job ID, which is passed from the parent view
    var jobID: String 
    
    // Define an environment object for the job store
    @EnvironmentObject private var jobStore: JobStore
    
    // Define a binding for the job
    private var jobBinding : Binding<Job> {
        Binding {
            // Get the job from the job store
            jobStore.jobs[jobID] ?? .init(id: "", title: "", tags: [])
        } set: {
            // Set the job in the job store
            jobStore.jobs[jobID] = $0
        }
    }
    
    // Define the body of the view
    var body: some View {
        // Create a text field for the job title
        TextField("Job title", text: jobBinding.title)
    }
}
```

### Rebinding:

```swift
struct ContentView: View {
    @State var location: String = ""

    var body: some View {
        let binding = Binding<String>(get: {
            self.location
        }, set: {
            self.location = $0
            // do whatever you want here
        })

        return VStack {
            Text("Current location: \(location)")
            TextField("Search Location", text: binding)
        }

    }
}
```

for text:  (ref:  https://stackoverflow.com/a/67624201/5389500 )

```swift
extension Binding {
    func onChange(_ handler: @escaping (Value) -> Void) -> Binding<Value> {
        Binding(
            get: { self.wrappedValue },
            set: { newValue in
                self.wrappedValue = newValue
                handler(newValue)
            }
        )
    }
}
now call change on the binding in TextField something like below.

  TextField("hint", text: $text.onChange({ (value) in
      //do something here
  }))
```

### Gotchas:
- Dont init state vars: https://stackoverflow.com/questions/56973959/swiftui-how-to-implement-a-custom-init-with-binding-variables
- you can add custom trigers in observable objects: "Observable Objects with Custom Publishers" https://medium.com/@TechSavvyScribe/swiftui-observable-objects-c7bf9bfc2062
- In preview you have to make your states static: `@State static var myCoolBool = true // Note: it must be static`
- ⭐ Rebinding: `@State private var firstToggle = false` and `let firstBinding = Binding(  get: { self.firstToggle }, set: {  self.firstToggle = $0 })` (you can also add hocks inside this rebindig code)
- ⭐ Wrapping a state: `@State var credential: Credential` and `self._credential = State<Credential>.init(wrappedValue: credential)`


### Resources:
- https://sarunw.com/posts/data-in-swiftui-1/
- https://sarunw.com/posts/data-in-swiftui-2/
- https://sarunw.com/posts/data-in-swiftui-3/
- Manipulating the Navigation stack https://stackoverflow.com/questions/57334455/how-can-i-pop-to-the-root-view-using-swiftui
- Apple wwdc video on dataflow: https://developer.apple.com/videos/play/wwdc2019/226/
- Using a dictionary as a @state property: https://stackoverflow.com/questions/60930172/how-to-use-dictionary-as-binding-var-in-swiftui?rq=3
- https://www.hackingwithswift.com/quick-start/swiftui/two-way-bindings-in-swiftui
- more on scenestorage: https://useyourloaf.com/blog/scenestorage-for-custom-types/
- Using EnvironmentKey: https://sarunw.com/posts/how-to-define-custom-environment-values-in-swiftui/
- dependency injection with environment: https://swiftwithmajid.com/2019/08/21/the-power-of-environment-in-swiftui/
- how state and bindings work under the hood: https://gist.github.com/AliSoftware/ecb5dfeaa7884fc0ce96178dfdd326f8
- FocusState: https://github.com/onmyway133/blog/issues/896
- https://swiftwithmajid.com/2020/04/08/binding-in-swiftui/
- Redux-like state container in SwiftUI. Basics.: https://swiftwithmajid.com/2019/09/18/redux-like-state-container-in-swiftui/
- uniflow: https://swiftwithmajid.com/2023/07/11/unidirectional-flow-in-swift/
- nice overview: https://stackoverflow.com/a/72846821
- https://www.hackingwithswift.com/quick-start/swiftui/whats-the-difference-between-observedobject-state-and-environmentobject
- https://cocoacasts.com/what-is-the-difference-between-stateobject-and-observedobject
- https://onmyway133.com/posts/how-observableobject-work-in-swiftui/
- How to do didSet for State and Binding in SwiftUI https://onmyway133.com/posts/how-to-do-didset-for-state-and-binding-in-swiftui/
- How to unwrap Binding with Optional in SwiftUI https://onmyway133.com/posts/how-to-unwrap-binding-with-optional-in-swiftui/
SwiftUI DataFlow research  https://developer.apple.com/videos/play/wwdc2019/226/

### Todo: 
- write about observable: https://swiftwithmajid.com/2023/10/03/mastering-observable-framework-in-swift/

```swift
// Define a view model for user login
@Observable final class LoginViewModel {
    // Define variables for the user ID and PIN
    var userID = ""
    var userPIN = ""
    
    // Define a variable for the login status
    var isLoggedIn = false
    
    // Define a function to toggle the login status
    func loginToggle() {
        isLoggedIn.toggle()
    }
}

// Define a view for user login
struct LoginView: View {
    // Bind the view to the login view model
    @Bindable var viewModel: LoginViewModel
    
    // Define the body of the view
    var body: some View {
        VStack {
            // If the user is not logged in, display the login fields
            if !viewModel.isLoggedIn {
                TextField("User ID", text: $viewModel.userID)
                SecureField("PIN", text: $viewModel.userPIN)
                
                // Button to toggle the login status
                Button("Login") {
                    viewModel.loginToggle()
                }
            } else {
                // If the user is logged in, display a welcome message
                Text("Welcome, \(viewModel.userID)")
            }
        }
    }
}
``` 