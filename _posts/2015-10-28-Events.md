Delegation vs NsNotificationCenter vs Custom Event system<!--more-->

**Delegation**
There are two different delegation types: closure and protocol. basically the difference is that closures passes a method of a certain method signature to the delegate variable of the class you want to listen to. The protocol works the same way but instead of passing a method signature you pass a instance reference that complies to a common protocol. Bubbling functionality up in the parent hierarchy is cumbersome and riddled with pitfalls. You also need to setup every method in the protocol even if you only want to listen to one method. 

**NSNotificationCenter**
A one to many communication class. The problem here is if you need to send lots of events to lots of listeners, then the notification center becomes a bottle neck. Since every event needs to be checked against every listener. There may be some really fast caching system combined with a really fast database system keeping tabs of all the event listeners, but there are limits to how efficient you can make this. ==Its possible to narrow the scope of NSNotifications. So it may actually be a viable solution for event handling. Its narrow scoping is currently being used in the SelectorGroup and button component.==

**Custom event system**
The system I'm thinking about would support events being handled one parent at the time until it has reached its root parent, unless it was stopped at a parent level. Classes would need to comply to the IEventObserver in order to work with events. One could also setup an extension that could inject it self into classes that cant extend EventObserver. Like an imaginary SomeButoon class that would extend NSButton. We could then just setup an extension to SomeButton that would comply with IEventObserver. One could also set events to bubble or not bubble up the parent hierarchy. Stopping events from bubbling would be done by a method in any parent listening to the event. The drawback of this system is that each class that wants to listen to an event needs to subclass or have an extension that complies to the IEventObserver protocol. And would need to have a call to a method setting up an addEventObserver method and also a removeeventobserver method. Every parent wouldn't need to comply to the IEventObserver protocol, one could just skip that parent and keep walking up the hierarchy. But the first version would need each parent to comply to the protocol. To make things simple. 

**Conclusion** (for now):   
With NSNotificationCenter you can observe single class instance for events. That way you can narrow your observation scope ==significantly== by setting the object to the class instance you want to observe and also a constant variable for the name to observe. Button.down for instance. For lists with many items you can delegate the mouseDown event to a single method and have that dispatch information about the event, rather than adding an observer for every item. See example in the Button and SelectGroup 

**Discussion:**  
NSView's hitTest: Returns the farthest descendant of the receiver in the view hierarchy (including itself) that contains a specified point, or nil if that point lies completely outside the receiver. and -hitTest (NSView). Going up to your root view... Which means you could use mouseDown method for interaction handling in NSViews. And avoid having to dispatch notifications from the button. Then again you would need to implement releaseInside and releaseOutside code if you wanted the functionality of them. So better add event dispatching to the buttons as well. And actually remove calling super of mouseDown, to make things simpler (you may need theme in the future but then you just add them). Then again release inside and outside is just one shorthand if statement. So this could be done. then again a controller not implementing NSDelegator wouldn't be able to listen to the mouse events. unless it was complying with  NSDelegatable and it would also need to be added as a responder. (research this.) It would seem that just dispatching an event from Button would be the easiest option. If however in the future, you discover that it was to cpu heavy etc. you could revert to a Delegator style approach. and only use notification as a way to communicate less frequent communication

- [ ] Test if you can repackage NSEvents in the mouseDown method and add userInfo to it, this may enable event inspection, origin etc. 
- [ ] Write pro con for each methods
- [ ] Research KVO (Key-Value-Observation)
- [ ] Make a delegate with interface example and post on twitter and github
- [ ] Figure out how to use NSResponder with custom Events
- [ ] Research GCD GrandCentralDispatch
- [ ] Maybe make your own event system based on the need of the "Main project" and with the design of the observer pattern?
- [ ] take a look at this [apple docs](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html) 
- look for drag and drop and the origin of the event 

<img width="2144/2" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-11-22 at 16.04.19.png">


**NSNotificationCenter vs Delegation** analysis: [here](http://www.andrewcbancroft.com/2015/02/05/nsnotificationcenter-vs-delegation-analysis/)   
[here](http://blog.shinetech.com/2011/06/14/delegation-notification-and-observation/) 


**NSNotificationCenter**  
https://www.andrewcbancroft.com/2014/10/08/fundamentals-of-nsnotificationcenter-in-swift/

**Event system as extension** (seems too complex):  
http://flexmonkey.blogspot.no/2015/07/event-dispatching-in-swift-with.html

**scottlogic event system** in swift:   
http://blog.scottlogic.com/2015/02/05/swift-events.html 

- **SterlingEvent system** in the actionSwift project
try these: https://github.com/craiggrummitt/ActionSwift3/tree/master/ActionSwift3/com/actionswift/events

- **StephenHaney/Swift-Custom-Events** (GitHub project)  
https://github.com/StephenHaney/Swift-Custom-Events/blob/master/Events.swift

- **Five approaches to listening, observing and notifying in Cocoa.**
http://www.cocoawithlove.com/2008/06/five-approaches-to-listening-observing.html