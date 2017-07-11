Notes on GestureHUD<!--more-->. Great for debugging gestures in macOS

<img width="192" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/multitouch.mov.gif">


```swift
class TestView:NSView(){
    lazy var gestureHUD:GestureHUD  =  GestureHUD(self)
    init(){
        self.acceptsTouchEvents = true/*Enables gestures*/
        self.wantsRestingTouches = true/*Makes sure all touches are registered. Doesn't register when used in playground*/
    }
}
extension TestView{
    override func touchesBegan(with event:NSEvent) {
        gestureHUD.touchesBegan(event)
    }
    override func touchesMoved(with event:NSEvent) {
        gestureHUD.touchesMoved(event)
    }
    override func touchesEnded(with event:NSEvent) {
        gestureHUD.touchesEnded(event)
    }
}
```