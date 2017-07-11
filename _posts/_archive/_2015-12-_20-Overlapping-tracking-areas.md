**Solution** (for the mouseOver / mouseOut problem)  <!--more--> 
All you have to do is hitTest from view you are in. if this is true: 

```swift
window.view.hitTest(window.mousePos) === self/*Sudo code*/
``` 

<img width="240" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/kldfjif329842899f932.gif">

What this code does is that it returns the view under the mouse position. Now all you have to do is setup a few if and else clauses to verify that your mouse is of or on the view. It works after initial testing. Example coming soon. Now this only works for NSView. CALayer still needs the root NSView to do its mouse handling. So we will use NSView scheme in the continuation of the project. 

[Gist](https://gist.github.com/eonist/537ae53b86d5fc332fd3)


**Testing Overlapping NSView with NSTrackingArea:** (not working)  
<img width="240" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/owirgo4i2.gif">

The problem with the above example is that the view that cover the view bellow doesn't cancel the hover interaction. As you would expect. In some-cases this could be intended behavior but most likely not. To differentiate the two behaviors one could imagine describing it as mouseRollOver and mouseRollOut and in the above example mouseEnter and mouseExit.

## **CALayer interaction**

**A possible solution:**
All windows have views, all views have layers. So place a generic NSView in all windows. The window we use could have this NSView placed inside it on init so we would just extend this window, call it "InteractiveWindow" The NSView named InteractiveView has a NSTrackingArea that handles all the mouse events for all subViews and sublayers. The root NSView would call Interactive methods: mouseDown,mouseUp,mouseEnter,mouseExited,mouseMove. Why do we do it this way? Because this is the only way to support mouseEnter/mouseExit that respects other views that may cover it. If a view is partly covered, we only want the top-most view to receive the interaction. This is also the only way to support CALayers with interaction. 

- [ ] DO a little more research/digging into covered up views and interactions. 
- [ ] Test mouseDown on your current TempNSView demo
- [x] What if we hittest from the window with the cur mouse pos. From the subview. refToWin.view.hittest(mousePos,nil), then assert if self == to the returned view? (this was it!!!)

A hit "sort-of" solution for CALayer: [link](http://stackoverflow.com/questions/1861961/pragmatic-way-of-handling-mouse-in-core-animation-layers) 

**HitTesting:**
Because the NSView has an unknown number of layers in its hierarchy, we cannot assume which layer is being clicked just by its location. Fortunately, CALayer has a -hitTest: method designed to solve
this issue. When CGPoint is passed to the root CALayer, it returns the ==deepest CALayer that the point falls within==. This enables you to quickly determine which CALayer has been clicked so that your app can act accordingly.


## Code: (same as in the gist link)
NOTE: this should also work on RoundedRects provided that you implement isWithinPath in the hitTest method override
```swift
import Cocoa


/*
let a = TempNSView(frame:NSRect(0,0,100,100))
a.name = "a"
addSubview(a)
        
let b = TempNSView(frame:NSRect(50,50,100,100))
b.name = "b"
addSubview(b)
*/

class TempNSView :FlippedView{/*Organizes your view from top to bottom*/
    var name:String = ""/*Makes it easier to test*/
    var isMouseOver:Bool = false;/*you should hit test this on init*/
    var hasMouseEntered:Bool = false/*you should hit test this on init*/
    override var wantsDefaultClipping:Bool{return false}//avoids clipping the view
    override init(frame frameRect: NSRect) {
        super.init(frame: frameRect)
        let trackingArea:NSTrackingArea = NSTrackingArea(rect: bounds, options: [NSTrackingAreaOptions.ActiveAlways, NSTrackingAreaOptions.MouseMoved,NSTrackingAreaOptions.MouseEnteredAndExited], owner: self, userInfo: nil)
        addTrackingArea(trackingArea)
    }
    /**
     * NOTE: this method is called from the root view if this view is the top-most visible view
     */
    override func hitTest(aPoint: NSPoint) -> NSView? {
        //Swift.print("hitTest")
        var pos = convertPoint(aPoint, toView: self)/*converts the p to local coordinates*/
        pos -= frame.origin/*converts the point from a global position to a local position*/
        //Swift.print("pos: " + "\(pos)")
        //Swift.print("containsPoint(p): " + String(NSPointInRect(pos + frame.origin, frame)))
        return NSPointInRect(pos + frame.origin, frame) ? self : nil
    }
    required init?(coder: NSCoder) {fatalError("init(coder:) has not been implemented")}
    /**
     * MouseMoved
     */
    override func mouseMoved(theEvent: NSEvent) {
        //Swift.print("mouseMoved")
        if(hasMouseEntered){/*Only run the following code when inside the actual TrackingArea*/
            let theHitView = window!.contentView?.hitTest((window?.mouseLocationOutsideOfEventStream)!)
            //Swift.print("theHitView: " + "\(theHitView)")
            if(theHitView === self){//mouse move on the "visible" part of the view
                if(!isMouseOver){mouseOver()}
                mouseMove()
            }
            else if(isMouseOver){mouseOut()}//mouse move on the "invisible" parth of the view
        }
    }
    /**
     * NOTE: draws a 100 by 100 square with a random color
     */
    override func drawRect(dirtyRect: NSRect) {
        Swift.print("TempNSView.drawRect()")
        let graphicsContext = NSGraphicsContext.currentContext()!
        let context = graphicsContext.CGContext/* Get the handle to the current context */
        let path = CGRect(0,0,100,100).path
        CGContextAddPath(context, path)
        CGContextSetFillColorWithColor(context,NSColor.random.CGColor)
        CGContextDrawPath(context, CGPathDrawingMode.Fill)
    }
    /**
     * MouseMove (only fires when the mouse is actualy moving on the visible  part of the view)
     * NOTE: It could be possible to only call this method if a bool value was true. Optimization
     */
    func mouseMove(){
        //Swift.print(name + " mouseMove")
    }
    /**
     * Only fires if the mouse is over the visible part of this view
     */
    func mouseOver(){
        Swift.print(name + " mouseOver")
        isMouseOver = true
    }
    /**
     * Only fires if the mouse is "rolls" out of the visible part of this view
     */
    func mouseOut(){
        Swift.print(name + " mouseOut")
        isMouseOver = false
    }
    /**
     * Fires when the mouse enters the tracking area, regardless if it is overlapping with other trackingAreas of other views
     */
    override func mouseEntered( event: NSEvent){
        //Swift.print("TempNSView.mouseEntered: ")
        hasMouseEntered = true/*optimization*/
        let theHitView = window!.contentView?.hitTest((window?.mouseLocationOutsideOfEventStream)!)
        //Swift.print("theHitView: " + "\(theHitView)")
        if(theHitView === self){mouseOver()}//mouse move on visible view
    }
    /**
     * Fires when the mouse exits the tracking area, regardless if it is overlapping with other trackingAreas of other views
     */
    override func mouseExited(event: NSEvent){
        //Swift.print("TempNSView.mouseExited:")
        hasMouseEntered = false/*optimization*/
        if(isMouseOver){mouseOut()}
    }
    /**
     * MouseDown (for testing purposes)
     */
    override func mouseDown(theEvent: NSEvent) {
        Swift.print("TempNSView.mouseDown()")
        //Swift.print("window?.mouseLocationOutsideOfEventStream: " + "\(window?.mouseLocationOutsideOfEventStream)")
        let theHitView = window!.contentView?.hitTest((window?.mouseLocationOutsideOfEventStream)!)
        Swift.print("theHitView: " + "\(theHitView)")
    }
}


```