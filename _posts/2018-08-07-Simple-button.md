Two approaches when making a button in swift, that isn't an UIButton<!--more-->.

### The Overriding approach

Using subclassing and overriding the already built in gesture recognizers. Instead of adding them again. Example of the latter can be seen on the bottom. 


```swift
/** 
 * let customButton:CustomButton = CustomButton.init(frame:CGRect.init(x:0,y:0,width:120,height:40))
 * addSubview(customButton)
 * customButton.tapUpInsideCallBack = {
 *    Swift.print("🎉")
 * }
 * TODO: Add onTapDownInside method
 */
class CustomButton:UIView{
   var tapUpInsideCallBack:TapUpInsideCallBack = defaultTapUpInside
   override init(frame: CGRect) {
      super.init(frame: frame)
      backgroundColor = .purple//Debug
      userInteractionEnabled = true/*⚠️️ might be needed ? */
   }
   /**
    * On tap down inside
    */
    override func touchesBegan(touches: Set<UITouch>, withEvent event: UIEvent?) {
       if let touch = touches.first, touch.view == imageView {
          /*touch began*/
       }
       super.touchesBegan(touches, withEvent:event)
    }
    /**
     * On tap up inside
     */
    override func touchesEnded(touches: Set<UITouch>, withEvent event: UIEvent?) {
       if let touch = touches.first, touch.view == imageView{
          /*touch ended*/
          onTapUpInside()
       }
       super.touchesEnded(touches, withEvent: event)
    }
}
extension CustomButton{
   typealias TapUpInsideCallBack = () -> Void
   static let defaultTapUpInside:TapUpInsideCallBack = {Swift.print("default \(CustomButton.self).onTapUpInside")}
}
```

### The Gesture approach 
Here is an example where we are adding GestureRecognizer to the view. Useful for more advance setups. 

```swift
/**
 * View that can be pressed like a button 
 * EXAMPLE:
 * let btn = ButtonView()
 * btn.onNormal = { btn.backgroundColor = .clearColor() }
 * btn.onPressed = { btn.backgroundColor = .blueColor() }
 * btn.onReleased = yourAction // Function to be called
 * REF: Code found on stackoverflow
 */
class ButtonView : UIView {
    var onNormal = {}/* Called when the view goes to normal state (set desired appearance) */
    var onPressed = {}/* Called when the view goes to pressed state (set desired appearance) */
    var onReleased = {}/* Called when the view is released (perform desired action) */

    override init(frame: CGRect)  {
        super.init(frame: frame)

        let recognizer = UILongPressGestureRecognizer(target: self, action: Selector("touched:"))
        recognizer.delegate = self
        recognizer.minimumPressDuration = 0.0
        addGestureRecognizer(recognizer)
        userInteractionEnabled = true

        onNormal()
    }
    /**
     * Gesture handler
     */   
    func touched(sender: UILongPressGestureRecognizer) {
        if sender.state == .Began {
            onPressed(self)
        } else if sender.state == .Ended {
            onNormal(self)
            onReleased()
        }
    }
    /**
     * Boilerplate
     */
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```