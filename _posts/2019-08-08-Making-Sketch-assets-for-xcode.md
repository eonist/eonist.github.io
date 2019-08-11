My notes on using sketch to make vector graphics for xcode projects<!--more-->

1. Figure out the size of the graphic you want to use: for instance 24x24
2. Make a square in sketch that is 24x24
3. Insert -> Artboard pick (set the artboard to 24x24 and set x/y to 0)
4. Click make exportable in the bottom right corner (set format to pdf, click export)
5. XCode: click the assets.xcasette and Drag the .pdf file into the left sideview
6. open the right sideview and set preserve vector to true, set scale to single scale
7. add the asset to code: `let image: UIImage = .init(imageLiteralResourceName: "nameOfResource")`
