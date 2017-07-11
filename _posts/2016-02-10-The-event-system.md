A simple event system that propagates events through hierarchical classes<!--more--> [Here](http://stylekit.org/blog/2015/10/28/Events/)  is the discussion that led to this event system

- 1. [Example](#example) 
- 2. [Notes](#notes) 
- 3. [Conclusion](#conclusion) 
- 4. [Tasks](#tasks) 

## Example:  

Also available on github as a [gist](https://gist.github.com/eonist/9c5345a53f75ff8aec07) 

```swift 
import Foundation

/*
* TODO: Implement the immediate variable if its needed (it would be a way to get assert the 1-level down immediate child an event came from, rather than the origin child which can be many levels deeper in the hierarchy)
*/
class Event{
    static var update:String = "eventUpdate"/*Ideally I would name this change but apparently then subclasses can name their const the same*/
    var type:String/**/
    var origin:AnyObject/*origin sender of event, this could also be weak if you discover a memory leak*/
    var immediate:AnyObject/*prev sender of event*/
    init(_ type:String = "", _ origin:AnyObject, _ immediate:AnyObject){
        self.type = type
        self.origin = origin
        self.immediate = immediate
    }
}
class ButtonEvent:Event{
    static var down:String = "buttonEventDown"
    static var up:String = "buttonEventUp"
}
class MouseEvent:Event{/*<--Sudo MouseEvent, the Real mouseEvent actually wraps an NSEvent*/
    static var down:String = "mouseEventDown"
    static var up:String = "mouseEventUp"
    var pos:CGPoint
    init(_ type:String,_ pos:CGPoint, _ origin:AnyObject){
        self.pos = pos
        super.init(type, origin)
    }
}
/**
 * All elements in the hierarchy that wants to propagate the event must implement this protocol
 */
protocol IEventSender:class {
    var event: ((Event) -> ())?{get set}//maybe rename to eventRelay?
    func onEvent(event:Event)
}
/**
 * Sudo version of the NSView
 */
class View{
    var superview:View?
    init(_ parent:View? = nil){
        superview = parent
    }
}
/**
 * InteractiveView facilitates sending of events when interactionEvents happens on the view
 * NOTE: its primary use-case is to wrap NSEvents into Event so that the view hierarchy can retrieve the view where the event came from which is not possible when using NSEvent. NSEvent cant be subclasses so this is the best solution I've found for this particular use-case.
 */
class InteractiveView:View,IEventSender{
    var eventCall:((Event) -> ())? {
        return {
            (event:Event) -> Void in if(self.superview is IEventSender){(self.superview as! IEventSender).onEvent(event)
            }
        }
    }//returns closure that will take care of propagating the event to the parent
    var event: ((Event) -> ())? /*this holds any method assigned to it that has its type*/
    
    override init(_ parent: View? = nil) {
        super.init(parent)
        //event = onEvent/*assign method to selector*/
        event = eventCall
        
    }
    /**
     * EXAMPLE: override onEvent in a subClass then assert origin === thumb && event.type == ButtonEvent.down
     */
    func onEvent(event:Event){
        //Swift.print("InteractiveView.onEvent: type: " + "\(event.type)")
        self.event!(event).setImmediate(self)//the setImmediate attaches the immediate instance to the event. 
    }
}
class A:InteractiveView{
    var b:InteractiveView?
    override init(_ parent:View? = nil){
        super.init(parent)
        b = B(self)
    }
}
class B:B2{
    var c:InteractiveView?
    override init(_ parent:View? = nil){
        super.init(parent)
        c = C(self)
    }
    override func onEvent(event: Event) {//override onEvent in this subClass
        //Swift.print("B.onEvent() ")
        if(event.origin === c && event.type == MouseEvent.down){Swift.print("B.onEvent() event.type: " + event.type + " pos: " + "\((event as! MouseEvent).pos)")}//we listen for events that is of c origin only and is of EVent type MouseEvent.down
        super.onEvent(event)
    }
}
class B2:InteractiveView{
    override func onEvent(event: Event) {//override onEvent in this subClass, we can listen to events etc
        //Swift.print("B2.onEvent() ")
        super.onEvent(event)
    }
}
class C:InteractiveView{
    override init(_ parent:View? = nil){
        super.init(parent)
    }
}
let a = A(nil)

func onEvent(event:Event){
    Swift.print("Root onEvent() origin: " + "\(event.origin)" + " type: " + "\(event.type)" + " immediate: " + event.immediate)
}

a.event = onEvent//lets hi-jack the event exit point of instance a. All events that are sent from a are now rerouted to onEvent method in root

let c = (a.b as! B).c!//Reference to the c instance
//imitate that an event was sent from instance c:
c.onEvent(Event(Event.update,c))//in root: type: eventUpdate
c.onEvent(MouseEvent(MouseEvent.down,CGPoint(x: 25,y: 25),c))//OutPut: in B type: mouseEventDown pos: (25.0, 25.0)
```

**NOTE:**  
The reason we are routing the event through the event variable is so that parents can choose how they want to listen to the event. The default behavior is through overriding, but if you rather want to listen to the event through the selector. Then you can. When you debug code sometimes its faster to just work within a method rather than setup class level variables, and override class level methods etc. 

## Conclusion:
we can do a whole slew of things with this super simple event system. Overriding in subClasses, ==hi-jacking== event exitPoints from parents etc. We can even Wrap the super strict apple event NSEvent and have it exit as nothing happened when the parent is NSView etc. So this system doesn't replace the NSEvent system it rather adds more features to it. You can pickup, stop and manipulate any event at any point in the UI hierarchy and also know where they came from. You can also pass any variables inside events. And you can also ==listen for events inside local scopes of methods==. by hi-jacking the event exit point. 


## More events:
here are some extra event types that one could use. I highly encourage you to make your own event types with your own needs and specs. The more events you define the easier they are to distinguish from one another.
  
```swift
class SelectEvent:Event {
    static var select:String = "select"
}
class KeyBoardEvent:Event{/*Sudo KeyBoardEvent, the real KeyboardEvent isn't made yet*/
    static var down:String = "keyBoardEventDown"
    static var up:String = "keyBoardEventUp"
    var key:String
    init(_ type:String,_ key:String, _ origin:AnyObject){
        self.key = key
        super.init(type, origin)
    }
}
class TextFieldEvent:Event{
    static var enter:String = "textFieldEventEnter"
    static var exit:String = "textFieldEventExit"
    static var change:String = "textFieldEventChange"
}
```

## Notes:
So a caveat with this approach is that you have to call the onEvent() to dispatch your event the same onEvent method that super classes may use to process events from the events in its children. Although you can call super.onEvent to get one super level deeper its still not as pristine as it should be. One way to mitigate this is to make sure you setup your event asserts properly. We could add variable that calls a method that calls the propagation closure, but then the event system starts to become complicated. Now its ==just 2 variables and 1 method==. Easy to add to other classes etc. Go with the current solution and maybe do the more complicated event system if the current solution is too cumbersome etc.

- Immediate is implemented as an ad-hock in the InteractiveView class. Immediate is also automatically set on event init. The same variable as origin is used.


## Tasks:
- [ ] Remember to add describable to the Event class, use extension to add this functionality
- [ ] Test if you can wrap Timer events this way. You need that for "the-finale-Symphony-gif-animation-of-StyleKit"
- [ ] Add the EventSender class to the example. You use it in CheckGroup and SelectGroup etc. 
- [x] Implement the immediate variable, its needed for CheckGroup and SelectGroup when working with CheckBoxButton and SelectTextButton etc
- [ ] Consider Using the event and onEvent for all mouseEvent methods as well. Less code is better. Maybe every mouse methods except mouseMove ==since mouseMove fires so frequently==
- [ ] Consider attaching the origin in the InteractiveView if origin is nil, this way you dont have to manually add origin every time you send an event.