<!--more--> 


### Representable Protocol

Apple provided you with five representable protocol to wrap UIKit/AppKit/WatchKit into SwiftUI.

```
UIKit/AppKit/WatchKit	Protocol
UIView	UIViewRepresentable
NSView	NSViewRepresentable
WKInterfaceObject	WKInterfaceObjectRepresentable
UIViewController	UIViewControllerRepresentable
NSViewController	NSViewControllerRepresentable
```

These protocols have a very same life cycle and methods, with a purpose to bring the reactive capability to UIKit/AppKit/WatchKit (I will use just UIKit in the rest of the post, but everything applies to all three).

```swift
struct ActivityIndicator: UIViewRepresentable {
    
    func makeUIView(context: Context) -> UIActivityIndicatorView {
        let v = UIActivityIndicatorView()
        
        return v
    }
    
    func updateUIView(_ activityIndicator: UIActivityIndicatorView, context: Context) {
        activityIndicator.startAnimating()
    }
}
// Use this view, and you will see the spinning indicator.
struct ContentView : View {
    
    var body: some View {
        ActivityIndicator()        
    }
}
```
You can expose startAnimating and stopAnimating in the form of binding value.
```swift
struct ActivityIndicator: UIViewRepresentable {
    @Binding var isAnimating: Bool
    
    func makeUIView(context: Context) -> UIActivityIndicatorView {
        let v = UIActivityIndicatorView()
        
        return v
    }
    
    func updateUIView(_ activityIndicator: UIActivityIndicatorView, context: Context) {
        if isAnimating {
            activityIndicator.startAnimating()
        } else {
            activityIndicator.stopAnimating()
        }
    }
}
```
more here: https://sarunw.com/posts/uikit-in-swiftui/