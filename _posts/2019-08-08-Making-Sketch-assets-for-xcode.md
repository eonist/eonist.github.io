My notes on using sketch to make and add vector graphics to xCode projects<!--more-->

### TL;DR
Sketch.app -> export **.pdf** -> add to `assets.xcasette` -> add to `UIImage...`

### Instructions
1. Figure out the size of the graphic you want to use: for instance 24x24
2. Make a square in sketch that is `24x24`
3. Insert -> Artboard pick (set the artboard to `24x24` and set x/y to 0)
4. Click make exportable in the bottom right corner (set format to **pdf**, click export)
5. XCode: click the `assets.xcasette` and Drag the .pdf file into the left sideview
6. Open the right sideview / attribute-inspector and set preserve vector to true, set scale to single scale
7. Add the asset to code: `let image: UIImage = .init(imageLiteralResourceName: "nameOfResource")`
8. Add dark/light mode by adding apperance in the right sideview

### Example:

```swift
let image: UIImage = .init(imageLiteralResourceName: "airbnb")
let rect = CGRect(origin: .zero, size: .init(width: 76, height: 76))
let imageView = UIImageView(frame: rect)
imageView.image = image
addSubview(imageView)
```

### Note:
It's also posible to use `.svg` Via this framework (for iOS and macOS):  [https://github.com/eonist/SVGWrapper/tree/main](https://github.com/eonist/SVGWrapper/tree/main) 