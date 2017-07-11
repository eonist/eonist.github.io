Debugging frame-animation and interaction <!--more--> It seems frame animation and interaction on the same nsview isnt straight forward. Here is an exploration into fixing it:

## brainstorming-log

1. When animating a method->
    Figure out which views this method will call and set the all to is animating

2. When directly manipulating ->
   If view isAnimating then cancel draw call

3. Here it is: (this isnt good for readability, but it will work)
if app displayLink is running
   All direct-manipulation must be added as an animation block. 
   And this animation block must Only fire in the next animation frame tick. 
   You can do this by packing your direct manipulation call in a closure 
   and adding it to  the frameTick call of displayLink.

4. **Or maybe...**
Every draw call that is not animating should be added to an animation tick, and the cvdisplay should start and end right after the 

5. **Better**	....(this avoids alot of complexities)<---this is it
Every draw call when direct manipulating and cvdisplayLink is animating
    Then add the drawcall to the animatiableview draw que and call it in the next tick
    
6. Start a new test where you change the size via direct manipulation and an on going alpha looping back and forth as a frame anim. Try to trigger the bug. 

7. Then try to trigger the bug by double calls via frame anim


8. Ok so mixing interaction that manipulates a CGContext while a frame-animation manipulates the same CGContext didn't work great. So I was thinking back to what apple does by default, and they actually commit every call to CGContext as an animation tick. I always found this strange. So I attempted something similar but a bit more lightweight:
What i did was that when you manipulate the CGContext directly via interaction you assert if the global CVDisplayLink is running. If it is then you delay the final setNeedsDisplay() call to the current frame tick in the CVDisplayLink. You do this by adding the final call to a global array that is looped through and called when the frame-tick happens. This scheme has the effect that all calls are done when the frame tick happens and not when the interaction event happens.
Since the interaction event probably doesn't sync to the frame refresh rate. After some stress testing (5min) this seems to work.



- [ ] Roll back the classes so they work again
- [ ] The bug could also be related to the fact that InteractiveView isn't implementing layer-hosted views the correct way. Even if graphic is. This will be easier to debug once you setup the interaction + frame animation test
- [x] Try the idea where you store direct manipulation draw calls if animation is in progress. And then call them in the next tick
- [ ] Add the cvdisplaylink to the window
- [ ] add more easing equations
- [ ] do research into global values and singletons