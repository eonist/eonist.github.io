Case study of Drawing shapes <!--more--> 

## Brainstorming log:

1. Performance was really bad when you scaled Graphic shapes up and set the contentsScale variable so that the graphic didnt pixelate. To fix this you need to probably have Shapes inside 1 view. so that whatever is drawn outside the frame isn't rendered. This means that you need to manage hierarchies yourself. Which means that you need to create CompositeShape class that can set the position of its descendants. But in reality all Shape instances that are added to a compositeShape is just added to the Page View. 

2. But you really need to look into what other people are doing. Just spend a day researching and writing things down. You have a solution that probably will fix it but research never hurt anyone. Check that swift wiki you created as well. Could find something there. 

3. actually just get svg import working and make that drawlab demo video you have in mind to put on drawlab and element

```swift
/*
 * Applies contentsScale to descendants of a view that has been zoomed (so that we avoid pixelation while zooming)
 * NOTE: maybe you can use a method in ElementModifier as it has similar code
 */
func zoomDescenants(view:NSView,_ zoom:CGFloat){
//for child in view.children
	//if child is IGraphic 
		//set fill.contentsScale = zoom
		//set line.contentsScale = zoom
		//graphic.draw()//Updates the graphic
	//if (child is NSView && child.children > 0)
		//zoomDescendants(child)
}
```

## Tasks:
- [ ] Import from SVG
	- [ ] PathUtils.paths() is under-developed and needs support for fillStyle and lineStyle
	- [ ] draw the example GraphicLib shapes in a translucent black window
- [x] create shape with fill
- [ ] create the canvas BG
- [ ] assert that the zooming behaves like it should 
- [x] Create the zoomTraverser method that sets the contentsScale value in all descendants of page
- [ ] add SelectPath select segments
- [x] setDisplayDrawInrect or similar method could solve performance problems with the current solution, nopp didnt work
- [ ] look into the resetScale method instead of doing this manually
- [ ] Create an extension for CGPath so you can do cgPath.lineTo(0,0) etc, much cleaner
- [x] try to load an svg that is a rect and has a gradient fill (complete)
- [x] try to load the above with a gradient line (complete)
- [x] try to load an ellipse (complete)
- [x] try to load roundRect (complete)
- [x] try to load a line (complete)
- [x] try to load all of the above at the same time w/ many different gradients (complete)
- [x] create the example .svg in illustrator (just copy the original values on github etc) (complete)
- [x] add the other curve types (See svg, as this works there) then try to load the magnification glass icon (complete)
- [ ] try to add the selectSegment lines in the SelectPath (complete)
- [ ] add bezier controls etc
- [ ] align the controlPoint better