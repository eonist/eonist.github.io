NSViews automatically receive mouse-clicked and mouse-dragged events, but because mouse-moved events occur so often and can bog down the event queue, an NSView must explicitly request its NSWindow to watch for them using NSWindow's //acceptsMouseMovedEvents: method. Also consider overriding acceptsFirstMouse, actually i think this just means that a button will receive a button click. 

A mouse event starts at the lowest NSView containing it in the view hierarchy (or, the topmost NSView displayed under the cursor), and proceeds up the responder chain through superviews until some object handles it.


An idea could be to have no frame in a NSView but implement hit-test instead. You could implement hit-test in an subview even. If you return the view in the hit-test then maybe mouseDown will work<--- important. 


**This is extremely important and could solve everything:**  
The NSView selected to receive a mouse event is determined by the NSWindow using NSView's hitTest: method, which returns the lowest descendant that contains the cursor location of the event (this is also the topmost NSView displayed). Once the recipient is determined, the NSWindow sends it a mouseDown: message, which includes an NSEvent object containing information about the click. NSEvent's locationInWindow locates the cursor's hot spot in the coordinate system of the receiver's NSWindow. To convert it to the NSView's coordinate system, use convertPoint:fromView: with a nil NSView argument. From here, you can use mouse:inRect: to determine whether the ==click occurred in an interesting area==, 

One thing to consider about the above paragraph is if the parent could catch the mouseEvent. Hopefully it can. 


**This may also be super important:**  
NSView's acceptsFirstMouse: method controls whether an initial mouse click is sent to the NSView or not. By default it returns NO, which in most cases is appropriate behavior. Certain subclasses, such as controls that don't affect the selection, override this method to return YES.


**This could be very important:**
You can also register an observer for the NSViewFrameDidChangeNotification (described below), and have it reestablish the tracking rectangles on receiving the notification. (Basically if you update the frame you can get an event call that it did, this could be important if frame needs to contain the point of the mouseevent etc)



HitTest you might want to override it to have a view object hide mouse-down events from its subviews. http://objc.toodarkpark.net/AppKit/Classes/NSView.html




**Useful:**
isDescendantOf:
`- (BOOL)isDescendantOf:(NSView *)aView`
Returns YES if the receiver is a subview, immediate or not, of aView, or if it's identical to aView; otherwise returns NO.



**This is the answer to that stackoverflow guy:**   
This kind of modal event loop is driven only as long as the user actually moves the mouse. It won't work, for example, to cause continual scrolling if the user presses the mouse button but never moves the mouse itself. For this, your modal loop should start a periodic event stream using NSEvent's class method startPeriodicEventsAfterDelay:withPeriod:, and add NSPeriodicMask to the mask passed to nextEventMatchingMask:. In the switch() statement the NSView can then check for a case of NSPeriodic and take whatever action it needs to; scrolling a document view or moving a step in an animation, for example. If you need to check the mouse location during a periodic event, you can use NSWindow's mouseLocationOutsideOfEventStream method. Ref here: http://objc.toodarkpark.net/AppKit/Classes/NSView.html


**This is the answer to the other stackoverflow question:**
the NSEvent objects generated for tracking events include a tag that identifies the rectangle that triggered the event. And To create a tracking rectangle, use the addTrackingRect:owner:userData:assumeInside: method. This method registers an owner for the tracking rectangle provided, so that the owner receives the event messages. This is typically the NSView itself, but need not be. The method returns the tracking rectangle's tag so that you can store it for later reference in the event handling methods, mouseEntered: and mouseExited:.   http://objc.toodarkpark.net/AppKit/Classes/NSView.html
