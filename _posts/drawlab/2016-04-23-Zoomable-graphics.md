
What I need: 
- Zoomable stroke,fill in Geom shapes
- Text that can scale infinitely with out becoming pixelated

NSScrollView: http://blog.helftone.com/infinite-nsscrollview/

callbacks in the NSScrollView: http://stackoverflow.com/questions/5169355/callbacks-when-an-nsscrollview-is-scrolled?rq=1

Scaling NSText: http://stackoverflow.com/questions/14113179/how-to-uniformly-scale-rich-text-in-an-nstextview

with autolayout: http://blog.bjhomer.com/2014/08/nsscrollview-and-autolayout.html


## Brainstorm log: (Subject: zooming/scaling graphics)

1. NSScrollView provides a lot use full methods to scale and scroll content. But its also limiting and you can achieve the same result with NSView and implement all the things that NSScrollView does and more. 

2. To get zooming to work with the Graphic class you need to provide each Graphic class the correct contentScale factor. The question is if you need/should use Graphic class at all. The point of the Graphic class is so that you can have subviews that are visible outside their parents frame. The question then becomes is this needed in a drawing app. You have layers and groups. Which can be set to move/scale/rotate in tandem. But this requires additional programming to manage these collection types. So it would be easier to just have it all nested in views like an hierarchy. Although it could be more difficult to manage clipping optimization. Its always easier to manage 1 view with many shapes. There is a reason illustrator has such a hard time with hierarchies etc. And it would be faster if you could have just shapes in one view? 

3. As in all cases of doubt: More information is usually the answer. Look at DrawKit and that ipad app that is opensource that does vector drawing. Ink or something. You need to do this at some point anyway. And awesome pages on github for links. 

4. The problem is that if you don't use the layer you have to have each view be very big. Which probably isn't very cpu friendly. And you have to draw every view from 0,0 even if they are subViews. So thats probably not event a possibility. 

5. ==If you set the contentScale dynamically as things scale and propagate the correct zoom factor down the hierarchies== then it will work. The additional code isn't a lot and not much code needs to change from your current implementation. Also you get a working result this way and can continue on. And you can always come back and do things more optimized. Also when you zoom/scale things down. say 50% of the original size you only need to draw half the resolution. and a quarter on when your at 25% and so on. This is really good Optimization when you zoom way out and see lots of things on the stage. 

6. Another alternative is to scale the path coordinates them selfs. and scale the stroke size as well. With this approach you would need to manage realSize and visualSize for each Graphic object. And for text you would need to scale the fontsize the same way. This would involve a lot of math calls and is probably not a good solution. Also when changing the stroke width when in a zoomed state, you would have to constantly multiply the stroke with the zoom factor. 

7. Maybe the best solution is to have one view that is just the size of the window. And then have many Layers or Views. Actually that begs the question: will setting the contentScale of the container layer also propagate that scale down to its children?

## Test suite for zooming/scaling:

1. MainContainer

2. RectGraphic

3. By playing with resizing bounds instead of the self.scaleUnitSquareToSize you may be able to control things better

4. or just figure out how much you need to increase the scaleUnitSquareToSize value in order to get to a zoom value of the init size. 

## IZoomable 
- when you set size of page or IZoomable. you need to store the realSize and then apply the zoom value to apply to the current size. 
- IZoomable must implement realSize and setSize. Now you don't need to implement this interface on DisplayPath etc. because they 
will be scaled by geometric entities aka the path will be scaled not the frame or bound.

- When you zoom in on Page. you need to inform all its descendants that one of their parents has changed their zoom and the contentsScale needs to change accordingly so that the graphic doesn't get pixelated. The contentsScale should be multiplied to the zoom amount of the parent that has been zoomed. Only one parent can be zoomed. But other parents can be scaled at which point the coordinates of the path of its descendants change and not the contentsScale. Such a traversing method needs to to traverse down the hierarchy from Page and look for instance of IGraphic And then apply the contentsScale to both fill and line shapes. Now you could argue that it would be better to have all DisplayPaths stored in page and then setup systems to manage layers and hierarchies. But traversing a hierarchy is as fast as traversing a 1 dimensional array. And you don't need to create the management system of layers etc. Calculating BoundingBoxes on hierarchies is also as fast. AS you can create efficient algorithms that determine if something should be rendered or not. Just append the offset of the parent to the edges to check against etc. 

## Tasks:	

- [ ] Can drawRect do GradientLine?
- [x] Can drawRect do CGContext? yes and you can use: drawInContext(ctx)
- [ ] Does NSText scale well inside NSScrollView
- [ ] Look more into DrawInk and DrawKit and awesomeSwift libs
- [x] Check if you get a bounds changed alert in Graphic if you scale any of its parents, you don't
- [x] check if bounds change in the lower graphic entities when you scale any of its parents, they don't. 
- [x] Get ControlPoint to stay the same size. By inverse scaling it in proportion to the page zooming in. 
- [ ] get scaleWithPivot working. By trial an error or by making a test in playground with drawrect