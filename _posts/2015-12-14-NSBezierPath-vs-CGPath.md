Seems simpler than its CGPath brethren, however I currently have the impression that CGPath has more support in quartz. <!--more--> For instance the case of gradient line uses heavly CGPath and at the current time of writing its unsure if Gradient Stroke can even be done with NSBezier. Keep it in mind though. One benefit of usingNSBezierCurve would be that it avoids adding the path to context multiple times when stroking and filling individually. Although quartx may do this anyway behind the scene. 

Example of the NSBezierPath simplicity:

```swift
//// Color Declarations
let color = NSColor(calibratedRed: 0, green: 1, blue: 0.478, alpha: 1)

//// Shadow Declarations
let shadow = NSShadow()
shadow.shadowColor = NSColor.blackColor()
shadow.shadowOffset = NSMakeSize(3.1, -3.1)
shadow.shadowBlurRadius = 5

//// Rectangle Drawing
let rectanglePath = NSBezierPath(rect: NSMakeRect(81, 35, 51, 54))
NSGraphicsContext.saveGraphicsState()
shadow.set()
color.setFill()
rectanglePath.fill()
NSGraphicsContext.restoreGraphicsState()

NSColor.redColor().setStroke()
rectanglePath.lineWidth = 6
rectanglePath.stroke()
```

Example:

```swift
let shadow = Shadow()
```