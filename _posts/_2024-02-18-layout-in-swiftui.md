My notes on laying out layout in swiftui<!--more-->


### ⭐ Sizing in SwiftUI (very important):
- ⭐ ideal size / intrinsic size / fixed size: https://sarunw.com/posts/intrinsic-content-size-in-swiftui/
- ⭐ FixedSize and ideal size: https://sarunw.com/posts/swiftui-fixedsize/ and https://www.hackingwithswift.com/quick-start/swiftui/how-to-make-two-views-the-same-width-or-height

### Stacks:
Equal height hstacks: https://sarunw.com/posts/swiftui-equal-height-hstack/

### Using dividers to create visual rythem 
https://www.theswift.dev/posts/swiftui-divider-colors-thickness
```swift
VStack {
    Text("Divider goes here")
    Divider()
        .tint(.orange)
        .foregroundColor(.green)
    Text("\"An element that can be used to separate other content\"")
}

```