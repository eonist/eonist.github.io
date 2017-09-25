Notes on GestureHUD<!--more-->. Great for debugging gestures in macOS

<img width="192" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/multitouch.mov.gif">


```swift
class TestView:NSView{
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

Look for the code here: [https://github.com/eonist/swift-utils](https://github.com/eonist/swift-utils)  (The file is named GestureHUD.swift)    


**Update 2017-09-07** By popular demand: I wrote an XCode AppDelegate.swift "starter kit" to get you started: 

[https://gist.github.com/eonist/2da348d2f29d02cca136be6476cf9710](https://gist.github.com/eonist/2da348d2f29d02cca136be6476cf9710) 


If the above AppDelegate code doesn't work out of the gate, check if you have gestures enabled in sys prefs. Restarting your computer or Restarting touch devices could also help