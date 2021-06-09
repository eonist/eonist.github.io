My notes on Compression, resistance, priority when using AutoLayout<!--more-->

## A quick summary of the concepts:
- Hugging: content does not want to grow
- Compression Resistance: content does not want to shrink

```swift
// Say you've got a button like this:
[       Click Me      ]
//and you've pinned the edges to a larger superview with priority 500.
// Then, if Hugging priority > 500 it'll look like this:
[Click Me]
// If Hugging priority < 500 it'll look like this
[       Click Me      ]
// If the superview now shrinks then, if the Compression Resistance priority > 500, it'll look like this
[Click Me]
// Else if Compression Resistance priority < 500,  it could look like this:
[Cli..]
// If it doesn't work like this then you've probably got some other constraints going on that are messing up your good work!
// E.g. you could have it pinned to the superview with priority 1000.
// Or you could have a width priority. If so, this can be helpful:
// Editor > Size to Fit Content
```

## Related:
- In conjunction with: `.lessThanOrEqual`, `.greaterThanOrEqual`, `.equal`
- constraint.priority (set minor and major constraints to the same view with priority of 999 and 1000 etc)

```swift
let minorWidth = Constraint.width(view, width: 200, multiplier: 1, relation: .greaterThanOrEqual)
minorWidth.priority = NSLayoutConstraint.Priority(rawValue: 999)
// Don't grow bigger than 400px
let majorWidth = Constraint.width(view, width: 400, multiplier: 1, relation: .lessThanOrEqual)
majorWidth.priority = NSLayoutConstraint.Priority(rawValue: 1000)
```

### Intrinsic Content Size
- Pretty self-explanatory, but views with variable content are aware of how big their content is and describe their content's size through this property. Some obvious examples of views that have intrinsic content sizes are UIImageViews, UILabels, UIButtons.
```swift
let horizontalHugging = view.widthAnchor.constraint(lessThanOrEqualToConstant: view.intrinsicContentSize.width)
horizontalHugging.priority = view.contentHuggingPriority(for: .horizontal)

let horizontalCompression = view.widthAnchor.constraint(greaterThanOrEqualToConstant: view.intrinsicContentSize.width)
horizontalCompression.priority = view.contentCompressionResistancePriority(for: .horizontal)
```
### Content Hugging Priority
- The higher this priority is, the more a view resists growing larger than its intrinsic content size.
- Content Compression Resistance Priority - The higher this priority is, the more a view resists shrinking smaller than its intrinsic content size.

```swift
// two labels, one should grow the other should shrink
label.setContentCompressionResistancePriority(UILayoutPriorityDefaultHigh, for: .horizontal)
label.setContentHuggingPriority(UILayoutPriorityDefaultHigh, for: .horizontal)
textView.setContentCompressionResistancePriority(UILayoutPriorityDefaultLow, for: .horizontal)
textView.setContentHuggingPriority(UILayoutPriorityDefaultLow, for: .horizontal)
// ref: https://stackoverflow.com/questions/42960649/uitextview-and-uilabel-aligned-in-a-row-in-swift
```

### Gotchas:
- If NSWindow is stuck or cannot be resized set the width constraint of subview to `w.priority = NSLayoutConstraint.Priority(rawValue: 499)`
 - there are two different methods, one can be set to a view, the other can be set to a constraint

 ```swift
 @available(OSX 10.7, *)
    open func contentHuggingPriority(for orientation: NSLayoutConstraint.Orientation) -> NSLayoutConstraint.Priority

    @available(OSX 10.7, *)
    open func setContentHuggingPriority(_ priority: NSLayoutConstraint.Priority, for orientation: NSLayoutConstraint.Orientation)


    @available(OSX 10.7, *)
    open func contentCompressionResistancePriority(for orientation: NSLayoutConstraint.Orientation) -> NSLayoutConstraint.Priority

    @available(OSX 10.7, *)
    open func setContentCompressionResistancePriority(_ priority: NSLayoutConstraint.Priority, for orientation: NSLayoutConstraint.Orientation)


```


### Resources:
- [https://developer.apple.com/documentation/uikit/uiview/1622485-setcontenthuggingpriority](https://developer.apple.com/documentation/uikit/uiview/1622485-setcontenthuggingpriority)  
- [https://medium.com/@dineshk1389/content-hugging-and-compression-resistance-in-ios-35a0e8f19118](https://medium.com/@dineshk1389/content-hugging-and-compression-resistance-in-ios-35a0e8f19118)
- Great example for two labels: [https://useyourloaf.com/blog/easier-swift-layout-priorities/](https://useyourloaf.com/blog/easier-swift-layout-priorities/)
- Compression explained: https://abhimuralidharan.medium.com/ios-content-hugging-and-content-compression-resistance-priorities-476fb5828ef
- Visual example hugging vs compression: https://gist.github.com/ftp27/c53e423bfc34e6baf22eee574a546f59
- Overview of constants: https://stackoverflow.com/questions/36924093/what-are-the-default-auto-layout-content-hugging-and-content-compression-resista
