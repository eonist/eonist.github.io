Research indicates that to solve the overlapping sibling View problem in OSX one needs to look into:<!--more-->    

1. CALayers (overriding the drawInContext seems to be the equivalent to drawRect) (drawLayer:inContext: could also be tested) (also look into CAShapeLayer) [link](http://stackoverflow.com/questions/466297/is-there-a-proper-way-to-handle-overlapping-nsview-siblings/34096780#34096780) 
2. Setting "WantsLayer = true" and overriding wantsUpdateLayer:Bool = false (the problem here is that the view gets clipped) more research needed

**NOTE:** The ordering of views works out great when you initiate an app, but as soon as you start updating individual views with new graphics, the sibling views will not overlap in the order it was initiated, but rather pop to the front if it has been updated. (this can probably be mitigated by overriding the mouseEnter method and not passing on the event)

**NOTE:**
If you setup the root NSView with "self.wantsLayer = true" the subsequent children siblings will not overlap on redraw, how ever now the  override var wantsDefaultClipping:Bool{return false}, which takes care of not clipping the view, doesnt work anymore. 
- [ ] Research wantsDefaultClipping and wantsLayer or wantsDefaultClipping and using CALayer

**NOTE:**
A problem has surfaced while working with overlapping siblings, the mouseOver event fires in areas that are covered by another element above it. This is undesired behavior.

**Discussion 1:**
So either keep using NSView and figure out the clipping problem, or figure out how to use CALayer with zero clipping. 

**Discussion 2:**
Maybe clipping isn't that important after all, think this through. If the children are clipped by their own frame, this poses a problem if you want to add a dropshadow to them. As dropshadows usually are outside the bounds of a child view frame. (It is important, think shadows,outside-line, animation)

**Discussion 3:**
It seems if you use CALayer you can set the "layer!.masksToBounds = false" and if you set the frame to an arbitrary size say 100px x 100px. this will cause the layer to be drawn with out clipping to the frame. Note that the root NSView will have to be set at a frame size that has space enough for its children. The problem left is to then sort out how to deal with mouse over,out,down, up its hit target etc. 

**Solution:**
The overlapping mouseover problem can be solved by not passing on the mouseover event (think next NSResponder)

**SIDE-NOTE:**
Note that if you want to draw shadowed **text**  that is both stroked and filled, you may need to use transparency layers to achieve the effect you most likely will want. See “Drawing with Transparency Layers (Pan- ther)” (page 522) for a discussion of transparency layers and shadows.

**NOTE:**
The CALayer has a method named addObserver, which may enable observing of the layer, similar to NSView
