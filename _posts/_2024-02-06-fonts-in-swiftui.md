My notes on fonts in swiftUI<!--more-->

### Font

Using UIFont in swiftUI
```swift
let uiFont = UIFont.systemFont(ofSize: 18, weight: .bold)
// 1
let font = Font(uiFont)
```
Apple encourages us to use built-in text styles whenever possible.
```swift
Text("Hello, world!")
    .font(.largeTitle) //  text style adapts its size based on user text size preference
``` 

There are two ways to set a font size for Text view in SwiftUI.

- Fixed size (Fixed font size stays the same regardless of user preference.) To set fixed font size, we specified the size we wanted at a time when we created a font. We can set this for both the system font and a custom font.
- Dynamic size: Dynamic Type size is a way we describe text size from its usage (text style) instead of the exact point size. Apple has 11 text styles at the moment.
1. Large Title
2. Title 1
3. Title 2
4. Title 3
5. Headline
6. Body
7. Callout
8. Subhead
9. Footnote
10. Caption 1
11. Caption 2


If the text style size doesn't match what your need, you can set a font size by specified when you initialize a font.

Set font size for a system font.
```swift

Text("Hello, world!")
    .font(.system(size: 36))
    
```

Set font size for a custom font. 
```swift
Text("Hello, world!")
    .font(.custom(
        "AmericanTypewriter",
        fixedSize: 36))

```

Note that once you decide to set your font size, you opt out of the benefits of text style automatic sizing based on user prefs. It is possible make relative fontsize: You can also set a size for custom font relative to text style with Font.custom(_:size:). https://sarunw.com/posts/how-to-change-swiftui-font-size/

```swift
Text("Hello, world!")
    .font(.system(.largeTitle, design: .rounded))
If you initialize a fixed-size font, you can set the font design like this:

Text("Hello, world!")
    .font(.system(size: 34, design: .rounded))
``` 

Set font-weight with font initializer

You can set this when you initialize a custom system font.

```swift
Text("Hello, world!")
    .font(
        .system(size: 34, weight: .bold)
    )
```

Set font-weight with fontWeight(_:) modifier

Text view also has a way to modify font-weight. You can set it with fontWeight(_:) modifier.

Since it is a Text instance method, we use this on a text, not a font.

```swift
Text("Hello, world!")
    .font(
        .system(size: 34)
    )
    .fontWeight(.bold)
    
```


Font and Text view also has another method to apply a bold font weight to the text, bold().
```swift

Text("Hello, world!")
    .font(
        .system(size: 34)
    )
    .bold()

Text("Hello, world!")
    .font(
        .system(size: 34)
        .bold()

    )

    
```
Set font-weight with weight(_:) modifier

Set font-weight via weight(_:) modifier. This is a Font instance method, so we use this on a font, not text.

```swift

Text("Hello, world!")
    .font(.largeTitle)
Text("Hello, world!")
    .font(
        .largeTitle
        .weight(.bold)

    )
Text("Hello, world!")
    .font(
        .system(
            .largeTitle,
            design: .rounded
        )
        .weight(.light)

    )
Text("Hello, world!")
    .font(
        .system(size: 34)
        .weight(.heavy)

    )
```

How to change SwiftUI font Color

We can set text color via a Text view modifier, foregroundColor(_:).
```swift

Text("Hello, world!")
    .font(
        .largeTitle
        .weight(.bold)
    )
    .foregroundColor(.pink)

Text("Hello, world!")
    .font(
        .largeTitle
        .weight(.bold)
    )
    .foregroundColor(.orange)

Text("Hello, world!")
    .font(
        .largeTitle
        .weight(.bold)
    )
    .foregroundColor(.yellow)
```

**Relative size on custom font:**
To make a custom font scale with the same scaling factor as Apple text style, we use Font.custom(_:size:relativeTo:) method. more here: https://sarunw.com/posts/how-to-change-swiftui-font-size/

Here is an example where I use custom font with the same size and use the same scale factor as Apple text style.

```swift

VStack {
    Text("Large Title")
        .font(.custom(
            "AmericanTypewriter",
            size: 34,
            relativeTo: .largeTitle))
    Text("Title 1")
        .font(.custom(
            "AmericanTypewriter",
            size: 28,
            relativeTo: .title))
    Text("Title 2")
        .font(.custom(
            "AmericanTypewriter",
            size: 22,
            relativeTo: .title2))
    Text("Title 3")
        .font(.custom(
            "AmericanTypewriter",
            size: 20,
            relativeTo: .title3))
    Text("Body")
        .font(.custom(
            "AmericanTypewriter",
            size: 17,
            relativeTo: .body))
}
```