Logging an element's style:

```swift
//getting style address of a button
ElementParser.stackString(button) 
//and
StyleParser.describe(StyleResolver.style(button))//TODO: write a blog-post about these methods when debugging
//and getting the address of a style:
SelectorParser.string(style.selectors)
```