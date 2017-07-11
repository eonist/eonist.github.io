Researching hit-testing of sub views <!--more--> ==This article is a work in progress.== Still implementing the research. ==The article will be cleaned up and condensed once the implementation is further along and tested/confirmed.== 

## Premises:
You need to be able to detect sub views and their hit boundaries. It needs to work in an interactive / animatable environment. 

<img width="512" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/slider_test_1.gif"> 
The above example shows how you can create your own ScrollBar component. Code for this is on github.

## Important

**IMPORTANT CONCEPT TO UNDERSTAND:**
There are two interaction schemes to consider:

1. mouse-down-hit-detection-scheme
Propagates upstream
<!--add explanation here--> 
2. mouse-move-hit-detection-scheme
<!--add explanation here-->
Dependent on NSTrackingArea rectangles to work(optimization)
3. hitTest-scheme
<!--add explanation here-->
Propagates downstream

<img width="515" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/upstream_vs_downstream242feef2q.png">

**IMPORTANT 1:**
Read the ==Core graphics animation book== front to back (you need to do this at some point anyway)

**IMPORTANT 2:**
Note that an NSView can have ==multiple tracking areas and you cannot update the old ones==, so if you need to update a tracking area, remove the old one and just add a new one. Don’t forget to release your old tracking areas, or you’ll end up with a nice memory leak (<--This may not be relevant for swift) in swift you may need to set old NSTtrackingArea instances to nil when they need to update. You would have to make an implementation that can add and remove NStTrackingArea instance to the Element view class.

**EXTREMELY IMPORTANT 1:**
If you hitTest a view that has a frame.size of zero the hit test wont go through. As apple thinks since there is no frame there is nothing to hit. The thing is that CALayer backed views doesnt need a frame size to show content. And so we need hitTest even if the frame.size is zero. One way to do this is to implement your own hitTest method. Loop through children and hitTest them as well. 

<img width="550" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/tracking_areaif22f.png">

**Test setup 1:** (to test theories)
1. setup 3 views with transparent color fills. (Use the graphics engine) 
2. nest these 3 views inside each other
3. create a 4th view that is standalone (this can act as a "partly-cover-up-view")
4. test the window?.mouseLocationOutsideOfEventStream, and see what i picks up

<img width="1024" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Interaction events sketch 47hdje738.png">

**Idea 1:**
- A possible solution would be to let the skin add a rect of its frame to the parent Element NSTrackingArea instance. NSTrackingArea instances can have multiple rectangles added to it. (this solution would also be really quick to implement) 
- When a skin changes it would need to update its NSTrackingArea rect. So a direct reference would be preferable in that sense.  

**Idea 2:**
NSResponder etc works on NSViews. Since skin is also a view you could try to employ a scheme were you use NSTrackingArea on the Skin as well and then pass the event up to the parent view were it would be handled as well.

**Idea 3:**
~~Do a similar scheme as SelectGroup~~ bad idea, would clutter up the Element class.

**Idea 4:** (looks promissing)
In the init of the NSTrackingArea there is ==The third parameter (owner)== specifies where the event messages should be sent to, this is usually referenced to self. But could maybe be set to The parent: the Element instance.

- The fourth and last parameter can specify some extra data which will be passed with every event (except for mouse move events, probably because of the frequency in which they are triggered), which doesn't matter because you only need info on mouse enter / exit. So this parameter we could specify which skin layer was "entered" / "exited" 

- Since the skin size and position may change on interaction/animation, the skin should be able to update its own TrackingArea this should not be the responsibility of the Parent element. 

- Apparently if you update the geometry of a view, the updateTrackingAreas will get called, Override this method and add the new tracking-area based on the bounds of the view. Bounds thats interesting. Isn't bounds the size of the view and its subviews?, ~~maybe we could use Bounds~~ in the element to calc the needed space for the tracking view. ==this could all happen in the updateTrackingAreas call in element==. (<----I think this is the solution) Then you let the hitTest inspector decide if it hit a skin or not. (<----~~really like this approach~~, pursue it!) Research bounds!! The bounds are just the bounding box of the frame. I.E if frame is offset then the bounds increases, or the frame is rotated then it also increases. It doesn't help figuring out the total bounding box of a view and its subviews. (maybe watch that apple session vid again?) (I really think you should read that Core anim book now, since creating a hit-test for animated objects seems to be the missing key here)
 
**Idea 5:**
You don't implement tracking-area on the sub-views. Instead you just inform the parent that the subview has changed its size. And then 

problem with this is rounded corners, do they return their view if you hit outside the round-corner but inside the views frame? ==This must be tested before going forward== The implication of this is that you will need to implement isPointWithinPath on each subview. (luckely apple has code for this, even support for hull-testing, think vector with holes in them) 

<img width="464" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/descendant_hover_42gs.gif">    

**Idea 6:**
Ok i got it. The skin views should each override the hitTest. Element it self should not. Then you just add the .
- Then you just implement the NSPoint in rect in the skin class. (test the round-corner problem though, you may need to use hit CALayer graphics instead of using math to do this. the later will be faster probably)
- one problem though is that the Element view cant be sized 0,0. so then you have the hitarea bounds problem again. 


**Idea 7:**
SubViews and sub CALayers should automatically attach them self in the bounding area that is considered interactive of their parent. 


**Observation 1**
If you override a hitTest then no subView will receive subsequent hit-tests. (forwarding the hit test is an option)

**observation 2**
~~If you override mouseDown then no subView will receive the mouseDown event~~ Not true. However the frame.size of the subView must be within the mouseDown event. If its less then no mouseDown event will be triggered on that view. 

**observation 3**
- hit-test is for determining which view / CALayer was hit. (override if you want to block)
- mouseMove/down/up etc is for determining where the mouse hit. 

**observation 4**
If you override hitTest and return self or nil then self will be the upmost hit view. If you return nil then the parent view will keep searching its children for a view that is the lowest in the stack. (note however that the sub-children of the child you returned nil will not be included in the subsequent search) 
- If you return self at any point the search will stop and that view will be the hit result.
- if you return nil the search will continue but not in the siblings of the view you returned nil 

<img width="537" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/sibling_solution2d23d.png">

**observation 5**
- rounded rects are hit even if you hit them on the outside of the rounded corner. Its treated as a normal rect. 

**Observation 6:**
- mouse down has nothing to do with NSTrackingArea. NSTrackingArea is used for mouseMove/mouseEnter/mouseExit only.
- mouseDown is only detected if the view.frame contains the point of the mouseDown event.
- mouseDown is only detected if the parent view frame contains the point of the mouseDown event (which means that you have to either increase the frame when subframes size changes or send mouseEvents from the windowView that has the size of the window as its frame and thus covers all mouseDown events.)

**Observation 7:**
The stroke of a path is not considered hittable. If you need to include the stroke of a path in the mouse-down-hit detection-scheme you have to ...I think draw outline the stroke. 

**Observation 8:**
NSView can have a frame.size of zero. The hitTest will fire and the mouseDown will be triggered. However that is not try for subsequent children views. Which you would need, think Lists, Scrollviews etc.  

Write about how you might do tracking area hitTesting on the skins them selfs, and have the mouseMove method implemented in the Element. I think this could be done, test it!

**Observation 9:**
If you hitTest and return nil the mouseDown method in that view wont be triggered. If you return self it will.

**Observation 10:**
MouseMove works on subview in views that has no frame.size if the subView has set a TrackingArea

**Observarion 11:**
nextResonder is a reference to the next view in the chain. If you are in a subView the nextResponder will be the parent and so on.	

**Observation 12:**
if frame.size doesnt contain the subviews in the current implementation of StyleKit then the subViews doesn't get the mouse down event. 

**Possible solution 1:**
So a view with a frame.size set to zero will receive hitTest and mouseDown if hitTest returns self. Now you could then manually ask your children for a hitTest. and they in turn could answer with a self if they are hit inside their geom. 

-Now how would this work on mouseMove? Well you do the tracking area as a bound of the skin in skin. you then set the owner of the trackingArea to be the parent, namely the Element. Then you just update the tracking area if the skin geom changes. 

-What does this solution offer? The Element can be whatever size it wants to be.
-Whats the problem with this solution? The children of Element will need to be manually passed the hitTest as well. 
-why is it so important to set frame.size to zero? well think about skins that animate outside the initial frame size etc.
- So you sort of have to manually trickle down hitTesting to the children. Which you also would have to do with MouseDown, which it may be able to do think nextEventMatchingMask 

==This is not a great solution though. Do more research!==


**Possible solution 2:**
MouseTracking works on subViews of views that has no frame.size. So detecting mouse down must also somehow work. Get it to work and its solved. It can be done by iterating children of a view and just passing the mouseEvent down. the same could be done for hit-testing as well, but i feel there is a better way. I feel its built in im just missing something. This solution works though so you could go with it and just move on.

**Possible solution 3:**
use addLocalMonitorForEventsMatchingMask for everything, as it doesn't require the parent to have a frame.size or hitTests overrides or mouseDown method overrides. It just listens to mouse down events. regardless of where they came from. Now what you need to do is to implement hitTesting. Is anything covering me? am i within the clickable area of my self. aka Boundary.  

- override hitTest in InteractiveView. This method will loop through children in its view and call hitTest. Then it will return the returned value of the first hitTest that isn't nil. (this is probably what apple does as well, if its not blocked by a zero sized frame)

- You could do the same with mouseDown etc as you did with hitTest, but wouldn't it be more efficient to use the eventListener. This is similar to the legacy code anyway.

- A problem with using addLocalMonitor... is that apples doesn't want you to use it. They say only use if there is no other way to solve your problem. And you have to takeCare of removing the eventListener as well. with: `removeMonitor` So maybe just implement a loop for both hit test and mouseDown/up. You would need to remove eventListener etc which makes the code cleaner. This source seems to confirm the theory: [here](https://groups.google.com/forum/#!msg/quartz-dev/M3fqjQ8fjBg/5Yqf1lhOL4MJ) 

**Possible solution 4:** (<---Very likely the final solution)
After another research sweep I figured out that if you override acceptsFirstResponder the you should override the public var. this was not the case but ==something started to work==. If you pass the hitTest to all view children in your view. And in the subViews you do the same, if a child view is the last descendant then return self. You may even pass it down to CALayers since they have HitTest() as well as isWithin() then all mouseDown, mouseUp will trigger at each descending view. Even if the frames.size of any View is set to zero. I presume the reason I have to forward the hitTest call is because the frame.size is zero. This isn't a big deal as you can do the forwarding in the InteractiveView. 

- Then you do the TrackingArea in the skins and assign the Parent Element as the owner. This way you can have all mouse related event handling in the Element instance. As it is already. 

- A really important observation about this approach is that the mouseDown event actually fires from the last descendant in the hierarchy, as opposed to hitTest which starts in the "crown" and walks down to the last descendant.(downstream event bubbeling) So you have to override the mouseDown and then call super.mouseDown(theEvent) in the override so that the parent in the chain gets the mouseDown call. (upstream event bubbling) ==Its basically like this: the view that returns self is the view that also will first fire off the mouseDown event.==  

- Another great thing about this approach is that you can forward the hitTest call to the all skins. If all descendants return nil in their respective hitTest method. Then there was nothing to hit and so no mouseEvent will be triggered at any stage.   The skins and the skins CALayer instances will be the last descendants to call. The line and fill Shape is the final descendants to call. Shape is a CALayer so it could implement hitTest and isWithin. However for now i think the skins could be the last descendant and return self if the point is within self.fillShape.path (this lat part could be optimized later with faster hitTest algorithms) The lineShape could also be hit tested in the future if this was needed. (the outline of the stroke would be cached probably) 

- If at anyPoint you want to block the mouseEvent from going upstream, you just don't call the super.mouseDown(theEvent) sometimes you do want the mouseEvent to propagate upstream, like hovering over the TitleBar, to reveal the icons. ==Or to set focus-ring to the entire List when a list item is clicked==

- This code snippet will give you a localPoint from a globalPoint (aka globalToLocal). (GlobalPoint in 0,0 window space) If you change the argument to "toView" then you get a "localToGlobal" point. `let localPoint = convertPoint(aPoint, fromView: self.window?.contentView)` <---(there might be a more correct way of doing this. But this works so moving on,and doesn't care about flipped views) you can also do the snippet bellow to get a similar result, im not sure if its faster or more correct, but its an option:

```swift
/**
 * Returns a correctly flipped coordinate of the mouse in window space 0,0
 */
var winMousePos:CGPoint {
    var pos = (window?.mouseLocationOutsideOfEventStream)!//convertPoint((window?.mouseLocationOutsideOfEventStream)!, fromView: nil)/*converts the p to local coordinates*/
    pos.y = window!.frame.height - pos.y/*flips the window coordinates*/
    return pos
}

//the you do this:

convertPoint(winMousePos, toView: nil)//<--try to replace toView with fromView if this doesn't work.
```

## Thoughts on hovering subviews:

<img width="537" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/event_propegation3d232.png">


Solution for roll over roll out

In the assert that determines if the graphic was hit or not. You have to ask is my akin hit or is my elemnt children hit. 

Is hit my skin -> then call roll over
Is hit not my skin -> then roll out

This should be the default behavior 


If you want element children to be apart of the roll over then you must override the enter and exit method and implement this behavior. 

Now the problem is a bit harder if you have nested children and say a btn 4 steps down in the stack is hovered and you want the crown parent to also hover but not say ui elements on level 2 and 3. To solve this you must think. BecAuse it might be important. Think focus ring etc. 

## Research transcript:

1. A solution were you store the hitView in a variable at each UI level would solve hover only on bg or on UI children. It doesn't solve if you want to hover a UI Element based on a pinpointed UI Element say only hover window on button at level 4 click. This is a rare case but it should be possible to target such things. This would also allow for a scheme were you could assign mouseDown in window only if "yes-button" at level 5 was clicked. "Targeting" there should be 2 targets: origin and immediate. Immediate would be the prev sender of the event, origin would be the origin of the event. 

2. You could possibly use hitTest to find origin (but this feels like double work for me) to find immediate you would have to use a loop that walks up the parent hierarchy while checking immediate != self. (it would be much simpler if these things could be stored in the NSEvent) ==But they cant it seems==

3. eventNumber is a unique number for each event and could be used as a way to store hitTest view and the eventNumber in a variable. Then you could just walk up and down the hierarchy to find the corresponding event. Although this doesn't feel like the right solution either (Think concurrency etc)

4. Custom CGEvent wrapped in an NSEvent, not working, could be because your missing the `@objc` infront of the subclass

5. CustomEvent based on a new class that does not extend NSEvent. Then just pass it up the hierarchy. (another reason for doing this would be that you could then also do the same with rollOver rollOut events. Since these events doesn't propagate up the hierarchy, this is important because what if you wanted to rollOver a button at level 4 in the hierarchy and have the background get the rollOver state, the only way to do this with the mouseEnter would be to add a variable named origin:NSView and then check if this origin is the button on level 4) So get Subclassing NSEvent working or make your own Event system that propagates up the view hierarchy.  (I think the reason that subclassing NSEvent doesnt work is because appkit detects that its not a pure NSEvent, maybe search for a way to override type checking of a class)

6. ~~Use a Swizzle technique to inject the userData into the NSEvent instance (this is probably the easiest, but also a little hacky) Cant use Swizzle~~, doesn't work with non-static variables

7. Continue searching for the best solution. Look at that codebase that used SinclairEvents and look around on github

8. if you decide to do your own event system remember to use weak variables for refs

9. ~~Hitch-a-ride-events: The concept is like this: you create a subclass of NSEvent named: Event and then you pass this Event only among InterActiveViews but you use the mouseEnter and mouseExited and mouseDown as usual. Not mouseMove since this is so frequent.~~ Nice idea if you could get the NSEvent.mouseEventWithType actually working this could be done with CGEvent as well but i cant seem to find mouseEnter event type for CGEvent

10. Another great use for custom NSEvents is using it for app Events as well. Instead of using the NotificationCenter as you currently do in the SelectGroup. ==In the spirit of moving forward== we will go with the Custom Event class and just propagate it up the hierarchy, then if you find better info on NSEvent and how to subclass it then just upgrade it to use that if its better. 

11. Thinking about it maybe we should implement a system of using hitTest, you could then make helper method to find immediate and origin views. It just feels like another layer of complexity to add another Event System. The reason im back on this idea is that if you think about it. MouseDown,Up,Over,Out,Enter,Exit doesn't happen that often. MouseMove does but we are not worried about that eventType as it never needs to do the hitTesting. 

12. Enter and Exit are only triggered when you enter or exit a trackingArea, and all trackingAreas are rectangular. What then with shapes that are nonRectangular? aka RounderRects or circles or path shapes. Now for mouseDown or mouseUp this is easy, but for hover this is another question. you could listen for mouseMove only if entering trackingArea has happened. this would limit the redundant and expensive mouseMove calls needed.  

13. to differentiate between enter trackingrect and enter tracking path you may need to implement an event system all the same. also how would you implement rollover on a button and have this event propagate to the window? actually that isn't that hard, you just add target as a parameter to the enter method. 

14. you need to add a target check to the mouseRollOver method. you could add view to the rollOver call. and then override the rollOver and then do the descendant check. do some more thinking with the flow chart. would this work? Godd thing the path hit test on hover is solved!

15. I am Window and I want to listen to rollOver/Out that happens on this button: btn1. 

16. I think that implementing a rollOver/out listener in window for button would be double work on the count that the same thing is also done in the button it self. Checking for rollOver/out should only happen for button in one place. The alternative would be to make a EventHandler that would mimick the rolver/out scheme in design. An object? a set of static methods? Think! Making a event system for just rollOver and rollOut wouldnt add that much bulk either since you implement these methods anyway. it would also be easier to implement suchs schemes on the count of the user. Window would only need to override rollOver(event:MouseEvent) and then compare `btn1 === event.origin` if true then setState to over.

17. The problem with propagating the mouseExited NSEvent is that you cant tell where it came from. You cant hit-test because the point that NSEvent returns is already outside of the view it exited from. You cant store the view because there may be an infinite number of children propagating the same event. You could use NSTrackingArea instance that NSEvent has but a view may have many NSTrackingArea instances. Actually that is interesting. Could it be possible to use the trackingArea to assert where it didn't come from and thus you could know enough. Think about this.

18. How would you find the origin on mouseExited? you would have to search the entire tree of UI elements to find the NSTrackingArea that matched. 

19. Implement the mouseExit(event:MouseEvent) which would propagate the exit event with origin, ==so that you can differentiate which exit and enter events came from self and which came from children.== 

20. Actually, you wouldn't need to have the origin when exiting. This would be handled by the rollOut event. You only need to check if the tracking areas isn't from self. (==this solution would work but the benefit?== I think the same amount of code) So just implement the mouseExit and mouseEnter methods and use them in the InteractiveView hierarchy. It looks cleaner as well. and you could probably avoid a few calls to the hitTest this way. However, it would mean double calls. Actually, just block the enter and exit calls from going upstream. To be honist, why not block all NSEvents from going upstream? 

21. Make sure drag works as you need it to concerning the slider. Also drag and drop should work. 

22. So if you enter a trackingArea and then exit both calls will always fire. However, if you go move the mouse pointer really fast, non of the events occur, the good news is that only one event cant occur. So roll over / out shouldnt be a problem. as you are either over or out when the exit event occur. Make sure that you use the enter exit position from the event that you pass to the rollOver/out. so that you can avoid another call to hitTest.

23. So thinking about it I think what we could do is to implement dual methods for every event method. and then pass the origin alongside the original event, this way we could use the NSEvent at exit points if needed. So even rollOver is basically just an enter event that also has an origin attached. This scheme is basically just rerouting events and adding some data alongside it. You could even package these events in another EventType. although that may become a memory leak. better to just pass the ref of the view. so you would block every nsevent, except mouseMoved if useTrackingAreaMouseMove is true. this could be added later if needed. And if you need to use NSEvent you just convert the new events to these NSEvents and send them to super as you would before. As long as the events are statically typed the garbage collection should take care of them. Use Weak ref in the event wrapper. It will look way cleaner with Event wrappers than event methods with multiple arguments. The example code snippets need to look clean or people wont use it. So do the wrapper idea. And revert to dual arguments or dual event dispatching if the wrapper doesn't pan out. Now you could create NSEvent from thin area at the exit points, but I tried this and it didn't work. And also you wouldn't get refs to the trackingarea etc. 

24. I think the solution is to block all NSEvents and redirect these into methods with the same method names (method overloading) and the pass the event wrapped inside a new event class "MouseEvent". The event would be weakly referenced so that garbage collection can take care of it later (avoiding memory leak) The benefit of doing it this way is ease of use. And the option to forward NSEvent at exit points in the UI stack. Its also very flexible as you can easily assert which of the subComponents you want to listen to based on origin. It also avoids complexities involving mixing NSEvent and MouseEvents along side each other (aka double dispatch) It has a clear and concise setup that is easy to debug. And future support for touchEvent etc is quite easy to implement, you also have access to all information that NSEvent provides, like keyModifiers, pressure, position etc. Easily extendable through MOuseEventExtensions. A nice thing about this setup is that all event assertion and logic is in the eventHandler methods of NSEvent and the MouseEvent handlers only forward the MOuseEvents to their respective InteractiveView Parents. Any assertion is done in subclasses of interactiveView. All events pertaining to the skin will be handled with a simple assert is origin local, else then its a descendant. all events are automatically propagated upstream in the UI hierarchy.

25. The solution described in paragraph 24 works. Tested for memory leaks as well. No leaks.  To support advance SVG keyframe animation you can always set a static hidden layer in CSS with a contour of the hitArea. to avoid flickering rollOver/out effect etc. 

26. To solve dragging you need a way to keep listening to mouseMove events while your mouse is down. You need to listen to a global mouseMove event. that works across UI components. Wasnt there a solution for this when animating? its addLocalMonitorForEventsMatchingMask for the MouseMovedMask, but it doesnt work while the leftMouseBUtton is down. So thats a no go. DO wider research. NSLeftMouseDraggedMask maybe? Yepp that did it. 

27. Just when you thought you had the best solution something in your peripheral view shows up: TrackingArea has something called userInfo. Which you can add an object to. And all MouseEvents has a ref to its origin trackingView. so basically you could add a ref to the origin in TrackingArea. This seems really cool but is not going to be pursued as the solution described in paragraph 24 works well. 

28. The idea of using a TrackingView as a way to hitTest multiple CALayers etc is a little overkill. This can be done similar to interactiveview when you implement skin as a pure CALayer primitive. Currently it is just a regular NSView and so it can simply be another interaciveView. Then all you have to do is implement the "immediate idea" and listen for events in the Element class that has the immediate is the instance skin. This is a great solution because its true to the legacy code. Implement a test with the test skin classes. so that you know it works. also test nested skins, as SVGAsset is nested and it also needs to work with this. 

29. So all views are now InteractiveView, its simpler that way, how performant? time will tell. So the solution in the future is to toggle adding caLayer and NSview as a skin to the Element instance, and use tracking area on CALayer with the element as its owner. Moving on. 
	
30. The event system is currently working well. Had to implement a hi-jackable variable so that classes that are not sbclassing NSView can also work. Like the SelectGroup or controllers etc. this is how it was done: all events are sent through the event variable, so that non-nsViews can hi-jack the eventstream (SelectGroup for instance) then `button -> onDown() -> event(SelectEvent(SelectEvent.select,self)) -> onEvent(parent.event(event))` Added IEventSender and EventSender to get this working. 

31. So right now you can either propagate Event instancess through NSView parents or propagate through the event selector. Not both. MouseEvents propagatee only though NSViews. The system is subjected to change in the future to allow for both propegation methods to occur. Or even many event listeners. For now it works fine. 

- [ ] add an universal method for events in Interactive view. "onEvent" or just event. Which you can use to send ButtonEvent, SliderEvent etc. then override onEvent and add this code `if(event.origin === closeButton && event.type == ButtonEvent.down){doSomething()}` (thinking about you could do the same with all mouseEvents, for now its fine but you could do it)
- [x] Test the upOutside upInside.
- [x] Implement adding and removing of TrackingView in Skin. See that stackoverflow answere for a good solution. Its in the link section.
- [ ] The next step would be to test with siblings, im pretty sure sibling works. 
- [x] And then dragging.
- [ ] Then also fully test origin solutions. like a focus ring for a Text UI element etc.
- [ ] Then implement the solution
- [ ] Try to implement stepper component
- [ ] Try to implement LeverStepper, 
- [ ] Try to implement LeverSpinner
- [ ] Drop the Post notification scheme, and implement upstream event bubbling instead

**NOTE:** 
One could even imagine implementing true enter and exit methods that rely on path hitTesting not trackingArea hitTest. (it would first use trackingArea then if its inside resort to path hitTesting, optimization) this will require further mangling of the code, its possible though. a few extra line of code. 

- [ ] Implement IInteractiveView 
- [x] ==try to make the enter / exit events fail==. Aka not receiving the out call. Make a fast movement with the mouse. It cant fail or else you may get stuck buttons. To solve stuck buttons there are option available. See legacy code. 
- [x] google mouseEnter on custom paths
- [ ] read the exact enter and exit description on the apple site. maybe they only work on trackingArea
- [x] ~~google non rect trackingArea tracking~~, you already have this solved.
- [ ] does mouseMove call hitTest? test this with print
- [ ] id you implement hitTest in the caLayer does this then 


## Tasks 1:
- [ ] Add a method that updates the tracking area if the geom changes in a skin, see this article how [here](http://stackoverflow.com/questions/11188034/mouseentered-and-mouseexited-not-called-in-nsimageview-subclass) 
- [ ] Test the NSTrackingArea scheme were the Skin uses its parent as the owner of the TrackingArea
- [ ] Implement a Skin that updates the trackingArea according to its shape.
- [ ] Try hitTesting the CALayer maybe it will work on RoundedRect without using the isInsidePath?
- [x] Test an isInsidePath implementation, Works!
- [ ] Test sibling hitTesting with the hitTest forwarding approach 
- [ ] Test looping children in the HitTest override
- [ ] Look at your closure code in the selector example you made before. Make the addLocalMonit... call a standalone private method, not a closure. call this method handleMouseDown
- [x] research ~~addGlobalMonitorForEventsMatchingMask~~ and addLocalMonitorForEventsMatchingMask <---==This is was it==
- [x] ==Test sending mouseEvents from WindowView down through its hierarchy == (this is important because its the only way I've found to pass mouse-down-hits to views that doesn't have a defined frameSize) It works but isn't an ideal solution
- [x] Gather more information on mouseEvents and hitTesting (also look through old notes)
- [x] can mouseDown work in a subView if the parent has no frame size?, it cant!
- [x] What you need to test is if a view can be 0 by 0 in size and still have the mouseDown work. or do you have to add a trackingArea. (you need enough frameSize to have mouseDown work, NSTracking has nothing to do with mouseDown)
- [x] also test if rounded corners are hit. 
- [ ] do the same test with a circle. (Use one from GraphicsKit) (its probably the same as roundrect)
- [ ] Test if the detection of hit is true when the stroke is GradientStroke. Since your implementation of gradient stroke sort of uses the outline of the stroke not regular stroke. (Do this when becomes important)
- complete your answer on stackoverflow with an update with rounded rect: [here](http://stackoverflow.com/questions/9986267/mouse-enter-exit-events-on-partially-hidden-nsviews/34283392#34283392) 
**Scrapped ideas:**
Hit-testing the entire view for every UI component and then letting the isPointWithin method take care of testing if the mouse is with-in any of the skins in the Element. The problem with this is that its not efficient (Total overkill) 

**Unconfirmed theories:**
1. One thing to make sure of is that if you've subclassed CALayer, its -containsPoint: method still returns YES if the passed point lies within the bounds of your layer. Otherwise, -hitTest: will ignore that layer. [here](http://stackoverflow.com/questions/1861961/pragmatic-way-of-handling-mouse-in-core-animation-layers) 

**Couplling discussion**
It wouldn't be considered coupling because only a copy of a rect would be passed. Although the coupling has already happened when skin knows about its parent. That being said. All NSViews knows about their parents after they have been added. So its not such a big deal. Parent child relationship coupling. The entire framework is coupled that way for now. And it doesn't have to be if you design it in a way that the child only knows about its parent once its been added to its parent. Since the drawing wont happen until OSX wants it to happen. Unless you set the parent explicitly for special in-heritage circumstances.

## Nice to know:

the `addGlobalMonitorForEventsMatchingMask` can actually track mouse clicks outside its window, think Popover modal panels that are actually windows etc. Or dismiss dialogs etc. Click outside to dismiss etc.

## Snippets:  

This is a way to add keyboard events to a View (its probably possible to add this through an override aswell)  

```swift
[NSEvent addLocalMonitorForEventsMatchingMask:NSKeyDownMask handler:^(NSEvent *event) {
  NSString *characters = [event characters];
  unichar character = [characters characterAtIndex:0];
  if(character == NSDownArrowFunctionKey) {
     NSLog(@"key down");
  } 
  return event;
}];﻿
```
This snippet will add mouseEventListeners to left and right click. (Its awesome but apple ==doesn't want you to use it, unless the same action can be performed another way==) Also you can replace the closure with a regular function, just remove everything between the curly brackets.   

```swift
let eventHandler = NSEvent.addLocalMonitorForEventsMatchingMask([.LeftMouseDownMask, .RightMouseDownMask], handler: { (event : NSEvent) -> NSEvent? in
    Swift.print("test " + "\(event)")
    //self.
    let theView = self.window!.contentView?.hitTest((self.window?.mouseLocationOutsideOfEventStream)!)
    Swift.print("theView: " + "\(theView)")
    return event
})
//Then when you're done tracking, you do:

NSEvent.removeMonitor(eventHandler);
```

This snippet will give you the localMousePosition in your view :   

```swift
let mousePos = event.locationInWindow//or this: (window?.mouseLocationOutsideOfEventStream)!
let localPoint:NSPoint = convertPoint(mousePos, fromView: nil)
Swift.print("localPoint: " + "\(localPoint)")
```

Assert if left mouse button is down:    

```swift
if(NSEvent.pressedMouseButtons() == 1 << 0){"left is pressed"}//you should add the bitwise stuff as a constant, MouseState.LeftMouseDown
```
## Tasks 2:
- [ ] Figure out the problem were hover state isnt changed when the UI item animates out. To trigger this press maximize button in the win title bar. there is some info on the net about this. this article may even have  a link. 
- [x] Read through your notes on NSView, Events, CALayer and hit-testing. 
- [ ] Move more articles from github wiki to github pages and merge articles that are similar
- [ ] Maybe you could wrap NSNotificationCenter in something simpler. Its kind of anti-user-friendly. 
- [ ] Also implement support for disabling interactivity on a View. On that view not its children.
- [x] Look into how you can add trackingareas to CALayers, is the CALayer returned in a hitTest for instance? yes, it is!
- [ ] rename GraphicsEngine to GraphicsKit, or GraphicsLib?
- [ ] AssetDecorator doesnt work if the only skin is an asset when hovering a button. Since the svg asset is removed on statechange. this didnt work in legacy code either. A workaround for the user is to have a transparent rect kin beneth the svg. this may need some adjusting in the button class. 

## Notable findings:
[Manual fullscreen implementation from apple docs](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CocoaDrawingGuide/AdvancedDrawing/AdvancedDrawing.html#//apple_ref/doc/uid/TP40003290-CH207-CJBFEEJA) 

[simple hit test example](http://coding-journal.com/cocoa-using-nstrackingarea/) 
- You can use `CGPathContainsPoint(path,transform,point)` to hittest non-rectangle shapes. Ole Begman even has an inverted solution for this [here](http://oleb.net/blog/2012/02/cgpath-hit-testing/)  
- Dicussion about updating the TrackingArea when the view moves not the mouse: http://stackoverflow.com/questions/8979639/mouseexited-isnt-called-when-mouse-leaves-trackingarea-while-scrolling?rq=1
- PostEvent (this could maybe be used to bubble events)[apple docs](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSApplication_Class/index.html#//apple_ref/occ/instm/NSApplication/postEvent:atStart:) 
- sendAction (this could be used to dispatch app events) [apple docs](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSApplication_Class/index.html#//apple_ref/occ/instm/NSApplication/sendAction:to:from:) 
- A list of cocoa classes that uses completion block methods: [here](http://cocoasamurai.blogspot.no/2009/09/guide-to-blocks-grand-central-dispatch.html) 

- window tool bar height [here](http://richardallen.me/2015/01/15/nstoolbar-height.html) 


**Look into:** (figure out their subtle differences)  

```swift
/* Type of tracking area.  You must specify one or more type from this list in the NSTrackingAreaOptions argument of -initWithRect:options:owner:userInfo: */
    public static var MouseEnteredAndExited: NSTrackingAreaOptions { get } // owner receives mouseEntered when mouse enters area, and mouseExited when mouse leaves area
    public static var MouseMoved: NSTrackingAreaOptions { get } // owner receives mouseMoved while mouse is within area.  Note that mouseMoved events do not contain userInfo
    public static var CursorUpdate: NSTrackingAreaOptions { get } // owner receives cursorUpdate when mouse enters area.  Cursor is set appropriately when mouse exits area
    
    /* When tracking area is active.  You must specify one of the following in the NSTrackingAreaOptions argument of -initWithRect:options:owner:userInfo: */
    public static var ActiveWhenFirstResponder: NSTrackingAreaOptions { get } // owner receives mouseEntered/Exited, mouseMoved, or cursorUpdate when view is first responder
    public static var ActiveInKeyWindow: NSTrackingAreaOptions { get } // owner receives mouseEntered/Exited, mouseMoved, or cursorUpdate when view is in key window
    public static var ActiveInActiveApp: NSTrackingAreaOptions { get } // owner receives mouseEntered/Exited, mouseMoved, or cursorUpdate when app is active
    public static var ActiveAlways: NSTrackingAreaOptions { get } // owner receives mouseEntered/Exited or mouseMoved regardless of activation.  Not supported for NSTrackingCursorUpdate.
    
    /* Behavior of tracking area.  These values are used in NSTrackingAreaOptions.  You may specify any number of the following in the NSTrackingAreaOptions argument of -initWithRect:options:owner:userInfo: */
    public static var AssumeInside: NSTrackingAreaOptions { get } // If set, generate mouseExited event when mouse leaves area (same as assumeInside argument in deprecated addTrackingRect:owner:userData:assumeInside:)
    public static var InVisibleRect: NSTrackingAreaOptions { get } // If set, tracking occurs in visibleRect of view and rect is ignored
    public static var EnabledDuringMouseDrag: NSTrackingAreaOptions { get } // If set, mouseEntered events will be generated as mouse is dragged.  If not set, mouseEntered events will be generated as mouse is moved, and on mouseUp after a drag.  mouseExited events are paired with mouseEntered events so their delivery is affected indirectly.  That is, if a mouseEntered event is generated and the mouse subsequently moves out of the trackingArea, a mouseExited event will be generated whether the mouse is being moved or dragged, independent of this flag.
```
