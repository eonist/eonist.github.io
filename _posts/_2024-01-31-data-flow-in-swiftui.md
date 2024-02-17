My notes on data flow in swiftui <!--more-->

 merge this with the other data flow post

### ⭐ Source of truth (Model)
Every piece of data that you read in your view hierarchy has a source of truth, and it should always have a single source of truth
**@State**

@State is a source of truth designed for use locally in a view. Since it means to use internally in the view, Apple recommends marking it as private to reinforce the idea that @State is own and manage by that view only.

**@Binding** is a suitable tool for any view mean to be reusable, since the view doesn't care where that data comes from; it just knows how to render according to that data. Most standard SwiftUI components using this, e.g. Toggle, TextField, and Slider.
```swift

public struct Toggle<Label>: View {
	public init(
		isOn: Binding<Bool>,
		label: () -> Label
	)
}

public struct TextField: View {
	init(
		_ text: Binding<String>
	)
}
``` 
To put this in use, you initialize your view like this.

```swift
@State var bar: Bool = false
   
var body: some View {
  Toggle("Toggle", isOn: $bar)     
}
``` 
We use $ sign to get Binding from @State, this also come from a help of property wrapper.

Everything we saw so far is considered internal data and event since it happens within a view. @State means to be local/private change within a view. @Binding declares a dependency on the @State. And actions we see so far are originated from a user interact directly with the view.

**ObservableObject protocol**

ObjservableObject is a protocol that SwiftUI provided to expose your object to the SwiftUI as a source of truth. Think of it as a tool to equip your object with a goodness @State get, but this time you manage the persistence storage yourself.

You use this when you want a reference type source of truth, which is great for the model you already have.

```swift
class Foo: ObservableObject {
  @Published var show = false
}
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
You conform your class to ObservableObject protocol and put @Published property wrapper on a variable that you want to keep track of the change. That's all you need to do to make your existing class working in SwiftUI.

@ObservedObject

Just like we declare a dependency on @State with @Binding, we use @ObservedObject to declare a dependency on ObservableObject.

You use it just like @Binding. The only difference is you use @ObservedObject with an ObservableObject. Which is suitable for the view that depends on a model object.

```swift
struct MyView: View {
	@ObservedObject var model: MyModelObject
}
```

MyView(model: modelInstance)
You can also get Binding from an individual property of ObservableObject with the following syntax.

`$model.property`

Which you can use with @Binding.

```swift
var body: some View {
  Toggle("Toggle", isOn: $model.booleanProperty)     
}
```
**@EnvironmentObject**

@EnvironmentObject is just another way of declaring a dependency on ObservableObject, but this time indirectly. With @ObservedObject you have to pass your data around hop by hop, which might feel cumbersome in some cases where that model might need to be consumed in many places. With@EnvironmentObject, you can inject that data from any ancestor view.

The downside of this is it won't be obvious on what ObservableObject needs to be set. To figure it out, you might need to go through the view hierarchy to see which object is needed for @EnvironmentObject. Failing to do this might cause run time error where @EnvironmentObject is not correctly set.

`contentView.environmentObject(foo)`
The entire hierarchy of contentView can access foo data by declare @EnvironmentObject

```swift
struct SomeViewDownTheHeirarchy: View {
    @EnvironmentObject var foo: Foo
    ...
}
```

SwiftUI also provided many environment values[4] you use, e.g., colorScheme, locale, sizeCategory. There might be a time when you need to adjust your view based on these values. When you want to do that, you can use @Environment property wrapper to reads a value from the view’s environment.

Usage

```swift
struct ContentView: View {
  @Environment(\.colorScheme) var colorScheme
	...
}
``` 

You can override this environment value by injecting it to any view just like @EnvironmentObject, and the entire view hierarchy will get that effect.

// An example to force view to be dark mode
`contentView.environment(\.colorScheme, .dark)`


### Binding:
@Binding is a Property Wrapper. You can think of it as a getter and setter of an underlying storage.

By default, the compiler synthesizes storage for the instance of the property wrapper by prefixing the name with an underscore (_).

So, if we have a property wrapper named @Binding var isAccepted: Bool, the compiler will synthesize the storage with the name _isAccepted. And the storage type of @Binding is Binding<T>.

In summary

Binding<Bool> is a type for underlying storage of @Binding.
This storage is named _isAccepted.
So to properly initialize it, we directly assign Binding<Bool> to the storage _isAccepted.
```swift
init(message: String, isAccepted: Binding<Bool>) {
    self.message = message
    self._isAccepted = isAccepted

}
```