My notes on using sketch to make vector graphics for xCode projects<!--more-->

1. Figure out the size of the graphic you want to use: for instance 24x24
2. Make a square in sketch that is 24x24
3. Insert -> Artboard pick (set the artboard to 24x24 and set x/y to 0)
4. Click make exportable in the bottom right corner (set format to pdf, click export)
5. XCode: click the assets.xcasette and Drag the .pdf file into the left sideview
6. Open the right sideview / attribute-inspector and set preserve vector to true, set scale to single scale
7. Add the asset to code: `let image: UIImage = .init(imageLiteralResourceName: "nameOfResource")`

### Example:

```swift
let image: UIImage = .init(imageLiteralResourceName: "airbnb")
let rect = CGRect(origin: .zero, size: .init(width: 76, height: 76))
let imageView = UIImageView(frame: rect)
imageView.image = image
addSubview(imageView)
```
