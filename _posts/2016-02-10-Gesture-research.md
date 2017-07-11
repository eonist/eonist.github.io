Notes on gestures <!--more--> 

Doubleclick gesture recognizer (el capitan only)
http://stackoverflow.com/questions/33225520/adding-click-handler-to-nstextfield-in-swift

[apple docs on gestures and multi touch tracking](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingTouchEvents/HandlingTouchEvents.html#//apple_ref/doc/uid/10000060i-CH13-SW10) 


Scroll view and gestures video: https://developer.apple.com/videos/play/wwdc2014-235/


this has some gesture tricks: http://www.raywenderlich.com/77975/making-a-gesture-driven-to-do-list-app-like-clear-in-swift-part-2



```swift
//
//set the `acceptsTouchEvents = true` in init of your NSView
 
override func touchesEndedWithEvent(event: NSEvent) {
    Swift.print("touchesEndedWithEvent: " + "\(touchesEndedWithEvent)")
}
override func touchesCancelledWithEvent(event: NSEvent) {
    Swift.print("touchesCancelledWithEvent: " + "\(touchesCancelledWithEvent)")
}
override func touchesBeganWithEvent(event: NSEvent) {
    Swift.print("touchesBeganWithEvent: " + "\(touchesBeganWithEvent)")
}
override func touchesMovedWithEvent(event: NSEvent) {
	Swift.print("touchesMovedWithEvent: " + "\(touchesMovedWithEvent)")
}
//there is also beginGestureWithEvent endGestureWithEvent
```


Gestures in IOS: https://www.weheartswift.com/bezier-paths-gesture-recognizers/
another one for ios: http://www.theappguruz.com/blog/gesture-recognizer-using-swift
third for ios: http://www.avocarrot.com/blog/implement-gesture-recognizers-swift/
fourth for ios: https://www.raywenderlich.com/76020/using-uigesturerecognizer-with-swift-tutorial


**Gesture Recognizers**

Gesture recognizers, which were added in OS X 10.10, provide a higher-level means of handling mouse input. If you have used UIKit’s gesture recognizers on iOS you will find them very familiar.
Each gesture recognizer is designed to recognize one specific gesture:
- NSClickGestureRecognizer
- NSPanGestureRecognizer
- NSMagnificationGestureRecognizer
- NSPressGestureRecognizer
- NSRotationGestureRecognizer
Gesture recognizers are added to a specific view and report the gestures using target/action, just like controls:

```swift
func setupPan() {
    let pan = NSPanGestureRecognizer(target: self,action: Selector("pan:"))
    someView.addGestureRecognizer(pan)
}
func pan(pan: NSClickGestureRecognizer) {
    let location: NSPoint = pan.locationInView(someView)
    switch pan.state {
        case .Began:
            Swift.print("Began panning at \(location)")
        case .Changed:
            Swift.print("Panning at \(location)")
        case .Ended:
            Swift.print("Ended panning at \(location)")
        default:
            break;
    }
}
```
Gesture recognizers are much richer than controls, however. The state property indicates the phase of the recognition.
Multiple gesture recognizers can be used together, but at times you will need to mediate between them. For example, you may need to differentiate a click from a double-click. Mediation is performed by implementing a delegate protocol:
```swift
class MainWindowController: NSWindowController, NSGestureRecognizerDelegate {
    @IBOutlet weak var someView: NSView!
    var click: NSClickGestureRecognizer?
    var doubleClick: NSClickGestureRecognizer?
    func setupClickAndDoubleClick() {
        let click = NSClickGestureRecognizer(target: self,action: Selector("click:"))
        click.delegate = self
        someView.addGestureRecognizer(click)
        let doubleClick = NSClickGestureRecognizer(target: self,action: Selector("doubleClick:"))
        doubleClick.numberOfClicksRequired = 2
        someView.addGestureRecognizer(doubleClick)
        self.click = click
        self.doubleClick = doubleClick
    }
    func gestureRecognizer(gestureRecognizer: NSGestureRecognizer,shouldRequireFailureOfGestureRecognizer other:NSGestureRecognizer) -> Bool {
        if gestureRecognizer == click && other == doubleClick {
            return true
        }
        return false
    }
    func click(click: NSClickGestureRecognizer) {
        if click.state == .Ended {
            Swift.print("Clicked!")
        }
    }
    func doubleClick(doubleClick: NSClickGestureRecognizer) {
        if doubleClick.state == .Ended {
            Swift.print("doubleClicked!")
        }
    }
}



//Note that you will need to check the state of the gesture recognizer in the action method, even for a simple gesture like a click, as it will first have the state .Began and then .Ended.
//Why would you use gesture recognizers over NSResponder’s mouse event methods? In an application with rich mouse event handling, the mouse event methods can become quite complicated. Gesture recognizers make it easier to segment code for different kinds of gestures. Another strong advantage is that gesture recognizers can be used in a controller – without subclassing NSView. ”



```

```swift
override func swipeWithEvent(event: NSEvent) {
        Swift.print("Swipe event.deltaY: " + "\(event.deltaY)" + " event.deltaX: " + "\(event.deltaX)")
        super.swipeWithEvent(event)
    }

```
NOTE: by listening for value == 0 you can detect end. which would reset start. and also give you change. BUt its better to use the magnify gesture listener
```swift
override func magnifyWithEvent(event: NSEvent) {
        Swift.print("Magnification value is" + "\(event.magnification)" + " event.momentumPhase: " + "\(event.momentumPhase)")
        var newSize:NSSize = NSSize()
        newSize.height = self.frame.size.height * event.magnification + 1.0
        newSize.width = self.frame.size.width * event.magnification + 1.0
        //[self setFrameSize:newSize];
        
        if(event.momentumPhase == NSEventPhase.Ended){
            Swift.print("the zoom ended")
            tempPagePos = CGPoint(page.x,page.y)
            previousZoom = zoom
        }else if(event.momentumPhase == NSEventPhase.Began){//include maybegin here
            Swift.print("the zoom began")
            tempPagePos = CGPoint(page.point.x,page.point.y)
            tempZoom = 1
        }else if(event.momentumPhase == NSEventPhase.Changed){
            Swift.print("the zoom changed")
            appendZoom(event.magnification);
        }
        
        super.magnifyWithEvent(event)
    }
```

- **Pinching movements** (in or out) are gestures meaning zoom out or zoom in (also called magnification).
- **Two fingers moving in opposite semicircles** is a gesture meaning rotate.
- **Three fingers brushing across the trackpad surface** in a common direction is a swipe gesture.
- **Two fingers moving vertically or horizontally** is a scroll gesture.


## Resources:
-  The PictureSwiper sample in the 10.7 SDK is a good place to start. (The 10.8 version of PictureSwiper uses NSPageController
- this link has a discussion on swiping: [here](http://stackoverflow.com/questions/12748072/how-to-properly-use-swipewithevent-to-navigate-a-webview-obj-c) 
- zooming with the DrawKit: http://apptree.net/gczoomview.htm