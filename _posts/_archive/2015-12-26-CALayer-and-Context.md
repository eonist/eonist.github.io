It seems you can only get the context of a calayer only within the drawInContext method or the displayLayer method. The downside of this is that you have to either use the CALAyer delegate <!--more-->  , which can only handle one delegate, or can it? ==why not handle 2 CALAyers with one delegate?== And then toggle between the initialize calls with an  if(layer == lineShape) else if(layer == fillShape) **This works so we will go with this for now**    

**The second way** to solve this is to utilize only 1 CALayer. and do the clipping of the stroke purely in Quartz.  

**The third way** to solve this is to create a Graphics class that stores all the drawing calls in an array, then pops the calls from the array when its ready to draw. This way of doing things could simplify The graphic and graphics class and its supporting code, but its also another level of complexity. 
- [ ] Research storing method calls and running them when desired. 

**The fourth option** would be to implement a clip variable in the Graphics class, if set the following quartz drawing would be clipped until end of drawing. Could also be a mask. (this option would be more in-tune with the original concept)
- [ ] research implementing mask in the current Graphics class. 
