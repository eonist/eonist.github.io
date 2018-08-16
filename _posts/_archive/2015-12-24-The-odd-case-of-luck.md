I was just testing the Graphic.swift class to implement the "new" css box model when all of a sudden the "old" css box model started to work in a layer-backed view. What was different from before was that the Graphic class did the quartz drawing inside the drawInContext and not in the drawRect as i was testing in Playground. <!--more-->  Then later it turned out that if you use layer-hosted NSView with NSView children ==and only use the layer-hosting for the drawing of the graphics in each NSView==, then there will be no Clipping. So to sum it up. Use NSView's with layer-hosting to have a unclipped hierarchy of NSViews. ==If you choose to use NSDraw rect in any of the sub-children you need to set the frame for this NSView only.== 

In this example: NSText and an NSView with regular drawRect is added to the lower-level square. then its offset outside the lowerlevel square to demonstrate the non-cliping approach. 

<img width="320" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-12-19 at 10.36.56.png">

In this example you have a layer-hosted skin. With a NSView child that also has a layer-hosted skin: The great thing about this is that now we can have outer shadow, focus rings you name it in the skins. And not have to worry about clipping. Children are not clipped to their parents etc. Not even by the window view? But what happens if you add NSText as a child? or a  NSView with drawrect? Does it start to clip again? and if so does it matter? Since Text doesn't need to reside outside its frame unlike outer shadow etc.  

<img width="320" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-12-19 at 09.47.55.png">

NOTE: a small cavity is that all drawing must be within its own frame. ==But at least its not clipped by its parent== like it was with the layer-backed approach. The frame of a drawing can be calculated based on the needed space on the fly so not a big deal. (just a little more code to handle the offsetting and fitting inside the frame)

- [x] Do a test with NSSKin with drawrect in your current setup
- [x] If the above task works then try NSText
- [x] Try to add a layer backed NSView to an layer backed NSView, and see if the later clips. If it doesn't then there is no reason to implement a hierarchy of CALayer at all. This is amazing and shows why you should cast a wide research net when in trouble. 
- [ ] test if the winview doesn't need to clip the frame-size as well
- [ ] Make sure that you don't have the same problem with overlapping siblings that you had before.
- [x] Complete the box model 
- [ ] Start testing with Element code (the fill shape and line shape shouldn't do any style setting of their own)
- [x] Test if you can add outer shadow to the Graphic instance.
- [x] Look into CAShapeLayer (Doesnt support gradient)
