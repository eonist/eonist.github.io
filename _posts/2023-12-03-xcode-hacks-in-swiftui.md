Xcode hacks in swiftUI<!--more-->

## Multi curor selection:

 - Hold ⌃ – Control and ⇧ - SHIFT then ↑ or ↓ to mark multiple lines
 - alt drag to mark multiple lines
 - multi select: shift + ctrl + mouse press


### No device
_PreviewNoDevice returns a view built from adding the previewLayout modifier and adding `padding to the input view:

```swift
struct _PreviewNoDevice<Value: View>: View {
   private let viewToPreview: Value
   init(_ viewToPreview: Value) {
       self.viewToPreview = viewToPreview
   }
   var body: some View {
       Group {
           viewToPreview
               .previewLayout(.sizeThatFits)
               .padding()
       }
   }
}
```
To use this view builder in a preview, simply pass in the View you're previewing:


```swift
struct CardView_Previews: PreviewProvider {
   static var previews: some View {
       _PreviewNoDevice(
           CardView(card: Card())
       )
   }
}
```

### Multiple devices
_PreviewDevices returns a Group containing a copy of the View for each device type. You can modify devices in the code to include the devices you want to see previews for:

```swift
struct _PreviewDevices<Value: View>: View {
   let devices = [
       "iPhone 13 Pro Max",
       "iPhone 13 mini",
       "iPad (9th generation)"
   ]
   private let viewToPreview: Value
   init(_ viewToPreview: Value) {
       self.viewToPreview = viewToPreview
   }
   var body: some View {
       Group {
           ForEach(devices, id: \.self) { device in
               viewToPreview
                   .previewDevice(PreviewDevice(rawValue: device))
                   .previewDisplayName(device)
           }
       }
   }
}
```
I’d be cautious about adding too many devices as it will make any previews using this view builder slow down and consume resources.

To use this view builder in a preview, simply pass in the View you're previewing:

```swift
struct ContentView_Previews: PreviewProvider {
   static var previews: some View {
       _PreviewDevices(
           ContentView()
       )
   }
}
```

### Combining multiple view builders
Each view builder receives a view and returns a new view. That means that you can compose the functions by passing the results of one view builder to another. In the extreme case, you can use up to three on the same view preview:

```swift
struct ContentView_Previews: PreviewProvider {
   static var previews: some View {
       _PreviewOrientation(
           _PreviewColorScheme(
               _PreviewDevices(ContentView())
           )
       )
   }
}
```
This produces 12 views to cover all permutations of orientation, appearance, and device.

For each view, you should consider which modifiers add value. For the CardView, it makes sense to use _PreviewNoDevice and _PreviewColorSchem e, but previewing on different devices and orientations wouldn't add any value.


```swift
@available(iOS 17, *)
#Preview(traits: .fixedLayout(width: 300, height: 300)) {
    ContentView()
}
#Preview {
    ItemsView(data: .empty)
}

#Preview {
    ItemsView(data: .error)
}
``` 

### resources: 

- https://swiftwithmajid.com/2023/10/17/mastering-preview-macro-in-swift/