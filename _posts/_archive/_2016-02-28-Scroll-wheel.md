My research notes on the scroll-wheel<!--more-->

<img width="185" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/CVDisplayLink_ScrollWheel.mov.gif">  
[vimeo](https://vimeo.com/157038820) and the original 60fps .mov file: [dropbox](https://dl.dropboxusercontent.com/u/2559476/CVDisplayLink_ScrollWheel.mov) 

- 1. [Example](#example) 
- 2. [Brainstorming log](#brainstorming-log)
- 2. [Notes](#notes) 
- 3. [Tasks](#tasks) 

## Example:
Coming soon

## Brainstorming log:
1. The scrollWheel event has ==momentum phase== and ==phase==. Momentum phase ends when the artificial friction ends. The phase ends as soon as the scrollWheel gesture stops. 

2. There are a couple of states the phase can be in: none, ended, may begin and changed. 

3. How to get a frictionless value from scrolling: An idea would be to use the `phase == ended || phase == none` to assert if the phase has stopped. Then we use the deltaY to derive direction. Basically setting a direction variable to -1 0 or +1. Then we use `phase == changed` to assert when to add or subtract a value to the scrollValue. 

4. So with this frictionless scrollValue we could start applying friction, velocity, gravity etc. Aka bounce / rubber-band motion / elastic bounce etc. 

5. We need some way of tapping into the call backs of app. 60fps or 30fps. This could maybe be done via NSTimer. NSTimer isn't that precise, but we don't need precision, ~~just average fps will work as well~~. Or we could use the callback of the CADisplayLink which should be 60fps and be precise. If you need 30fps ~~then just toggle each 60fps callback and its halved etc~~ there is a prop in CVDisplayLink you can set for this. 

6. Now that we have a way of measuring which way the scroll gesture happens in we then need to figure out how much velocity should be added to the current motion of the scroll-bar. This can be accomplished 2 ways the common denominator is that you do the calculation after the gesture event "on-up" is fired. The first way to calc the velocity is by calculating the distance between point-of-gesture-began and point-of-gesture-ended and then also start a timer when the gesture began and stop it when the gesture ended. This will give you the average speed of the motion. Think speed of a car can be 60mph(miles per hour) well the speed of your gesture motion is maybe 600 pixels per second. Then you divide the 600px by 60 to get a number that represents how many pixels your object needs to travel per frame. so 10px per frame is the speed of the animation. Now what if you then do many successive gesture motions. Like you want to scroll a page fast. Then you keep calculating the speed of the gesture motion and just adjust the speed of the animation of the object you want to animate. so if your last performed gesture motion was 15px per frame then set your velocity to 15px per sec instead of the current 10px per frame. You also need to sense when the scroll-gesture ended and the gesture was then "on-up". These need to happen in at the same time or the on up needs to happen before the gesture ended. Actually the phase state is `none` if the scroll gesture occurred and then released before "breaking" the motion. If the scroll-gesture also braked the motion then the phase-state is `ended` when onUp of the scroll-gesture. presumably the velocity can also be derived by taking the max value of the deviceDelta values during the gesture-phase so: `if(deviceDeltaY > maxVal){maxVal = deviceDeltaY}` you also need to take into direction into account here so assert if delta is negative or positive first then assert max or min accordingly. (note you have code for all of this, and you should look for simpler solutions than this, it could be worth pursuing though, since you probably need similar code in future ui animation schemes)

7. you still need to solve the question of the elastic rubber-band force above and bellow min and max boundaries. Basically rubber band force is stronger the greater the distance from the boundary the stronger the elastic force. the closer it gets to the boundary the less elastic force is needed. This is where the log10 equation [log10 anim] comes in ~~which you apply to the velocity of the mover object.~~ Apply to the value prop in the Mover instance. You then use ease-in to target, when you release the mouse or touch.  

8. Now you don't want to move the sliderThumb directly. Rather you want to resize the thumb height when the indicator is above min y boundary and offset the y and resize height if the indicator is bellow the max y. the amount is equivalent to the overshot. You use the last default size of the thumb. You may need to create an additional size property in  the ElasticThumb class. one to hold the default size derived from the size of content which it scrolls and one that is its actual size. basically its frame. 

9. You also need to stop updating the thumb once the motion has stopped. to not waste cpu cycles etc. then restart again on scroll-gesture etc. This is handled by the checkForStop in the Mover instance. Or you can also use the scrollWheel `event.momenthumPhase == "Ended"`

10. use phrases like elasticity, resistance, spring, density, velocity, gravity, force, friction, springiness, springSpeed, springBounciness, stiffness, damping, etc use this link for info on how to [Calculating the physics] <--this link has nice gesture and interaction anim research. you may also need to find some books on this subject to get this right.

11. you might not be able to extract the distance traveled of the gesture when using the scrollWheel. To get the distance another way you may try to take the sum of every value in the deviceDelta, which should not have the friction applied to it so it should represent real distance traveled. You might get the onUp location if you look into gestures in osx a bit more. You can test if you can get the distance traveled by testing with a circle and adding the deviceDelta x and y values.

12. 
**NOTE:** Alternate solution would be to use the friction scroll-values that apple provides and then overshoot the scrollbar thumb and then use a ==log10 calculation on a fps callback== ~~to bounce it back into position~~ the log10 only takes care of perceived friction offset. This seems like the simplest solution and the most cpu friendly. Although this is a simple solution it still leaves a  lot to be desired when it comes to animation and user interaction. So more exploratory research should be applied to this subject. Think momentum when your sliding slider buttons etc. 

13. ScrollWheel `event.phase == "Ended"` if release your touch-gesture and the momentum of the gesture has stopped. event.phase == "None" if the momentum hasn't stopped. 

14. ScrollWheel `event.phase == MayBegin` can be used to detect if two fingers are touching the trackpad. `event.phase == "Began"` can be used to detect when the scroll began. 

15. We cant use thouchesEndedWithEvent to detect the onUp event when there is momentum because the mouse doesn't post touch events, only touch-trackpads does. If there is no momentum we can use `event.phase == Ended`. A way to still detect the onUp event is to use a bool value to record if the `event.phase == None` has happened. And reset the same Bool val on `event.phase == MayBegin`

16. ==Never mind point 15.== The best way to detect onScrollWheelDown and onScrollWheelUp is to use `event.phase == MayBegin` for onDown `event.phase == Cancelled` and `event.phase == Ended` for onUp. This even works when your gesture goes off the trackpad and should work for MagicMouse as well. Test this later.

17. NSEventPhase.Stationary never fired so It couldn't be used. Instead the You can use the theEvent.scrollingDeltaY value. Store this value on scrollWheel change and assert that its not 1 or -1 in the ended event. This way you can assert if the scroll-wheel became stationary for a period without resorting to NSTimer etc. 

**Elastic pullback effect** when scrolling past the top or bottom boundaries.

1. When you drag or directly scroll above the top, use the [log10 anim] to offset the direct manipulation of the y position. Use the actual y value and the distance to the top.y then apply log10. This will result in an offset that slows down the further away from the top.y

2. When you release the page while above the top.y boundary you measure the distance from the offset.y to the top.y then you calculate the velocity based on the distance and the time you want this anim to take say 0.4sec. You could apply an ad-hock log10 ease out argument method to the Friction instance to get a nice ease in effect. 

3. When you reach the top.y boundary while in sliding motion. You should assert when you reach top.y and then start applying log10 until it reaches a standstill, then you start  to apply acceleration and a log10 ease out to the top.y position. 

Do more research into the above scheme

**swift:**  
  
```swift
//example code coming soon
```

## Notes:
Coming soon

## Tasks:
- [x] ==figure out how to get velocity from the scroll-wheel==
- [ ] add a better momentum algorithm. Use spring, friction, and use time and distance and record many trigger points
- [ ] Fix the problem when you scroll sideways and release. Seems to not cooperate with the stationary assert



<!--Links, this section wont be visible to the readers-->
[log10 anim]:https://medium.com/thoughts-on-thoughts/recreating-apple-s-rubber-band-effect-in-swift-dbf981b40f35#.tpbvwo1wi