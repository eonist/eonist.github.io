<!--more-->



``` swift
Text("Hello, SwiftUI!")
    .font(.headline)
    // 1
    .border(Color.pink)
```

Make a text view fill its container width with frame modifier

```swift
Text("Hello, SwiftUI!")
    .background(Color.orange) // only behind text
    .font(.headline)
    .border(Color.pink)
    //.frame(maxWidth: .infinity)
    // .frame(maxWidth: .infinity, alignment: .leading) // left aligned
    // .frame(maxWidth: .infinity, alignment: .trailing) // right aligned
    // .background(Color.indigo) // behind frame
    .frame(
        maxWidth: .infinity,
        maxHeight: .infinity

    ) // To make a view taking full height, instead of setting maxWidth to .infinity, we set maxHeight to .infinity.
    .border(Color.blue)
```