<!--more--> 

**This css:**  
```css
Element{
   fill:red;
   drop-shadow:drop-shadow(0px 0 #000000 0.4 14 14 1 2 true);
}
```
**Combined with this tiny bit of pure swift code:**  
```swift
StyleManager.addStyle(css)
self.addSubview(Element(200,200,20,20))
```
**Produces this result:**    
<img width="333" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-12-09 at 06.46.10.png">


**This is an isolated quartz test:**  
<img width="243" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-12-06 at 07.51.59.png">


- [x] Complete your "wide-net-research" into "inner/inset shadow" in quartz
- [x] take a look at your quartz research paper again, you have some shadow stuff there
- [x] investigate if you can make the shadow stuff a .filter of your graphic or graphics class, so that adding shadow becomes trivial.
  - [ ] Or simply .filters, where you could as many filter instances you wanted. Blend, DropShadow, Glow,blur,reflection etc. 
  - [x] For now just add .dropShadow to the Graphics class. 
- [x] make sure the inner shadow works with a gradient drawing as well, not just solid colors
- [ ] Investigate the idea that using stroke instead of the current blend scheme, one can achieve the same result.
  - [ ] The idea being that you can just create the shadow on a stroked path with the color set to clear on the stroke, then clip the shadow to the path
  - [ ] This is important because you may want to add blend modes to the graphics as a whole. And then this will interfere with that defines
  - [ ] Make a simple test where you stroke a path and add a shadow to it, take it from there and do the clipping and adding of a filled shape etc later, then make a process out of it similar to the one you have now
- [x] make support for many shapes (trenasperancy layer i presume)
- [x] Do a test where you both stroke and fill a shape and then add an inset dropshadow ot that result
  - [x] The stroke needs to be applied after the inset dropshadow was applied, or else the dropshadow will over-shadow the stroke, so to speak. the implication of this is that one can not use the strokeFill call, but needs to sandwich the dropshadow in between a call to fill and then again a call to stroke. This is consistent with how quartz probably does this anyway so no optimization is lost. 
- [x] Write a action diagram of how inset-shadow vs shadow vs no shadow should sandwich the fill and stroke
- [x] Re-examine the code from the old element project and 
**Action diagram of the various shadow modes:**

**No shadow** (we could do .FillStroke here but for consistency, and simplicity of the code structure we dont)  
1. addPathToContext  
2. fill  
3. addPathToContext  
4. stroke  

**Inset-shadow** (only the fill should receive the inset shadow)  
1. addPathToContext  
2. fill  
3. addPathToContext  
4. initClipping  
5. SetShadow  
6. SetBlendMode (makes the shadow look like an inset shadow)  
7. endClipping  
8. addPathToContext  
9. stroke (the stroke should be over the fill and the inset shadow applied to the fill)  

**Drop-shadow** (only the fill should cast the drop-shadow, the stroke should hover above the fill and not cast a shadow)  
1. addPathToContext  
2. SetShadow  
3. Fill  
4. endGraphicState (ends the dropshadow casting)  
5. addPathToContext  
6. stroke  

**NOTE:**   
The common denominator of the different shadow-modes above is the addPathToContext and its subsequent call to fill and stroke. To support shadow, what you do is that you seperate the stroke related cases from the main switch method in draw() and the fill related cases. Add an if clause that calles the Utiliterian shadow methods that will handle the applying of the shadow. Then you refactor down to the simplest and most intutive worflow. Simple and elegant. 



Example of shape with color fill and color stroke with inset shadow applied to the shape as a whole:  
<img width="240" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-12-07 at 18.04.32.png">

Example of many shapes using the same inset shadow scheme:  
<img width="262" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-12-07 at 15.36.53.png">

Lets first define what the drop-shadow css code should look like:   

```css
InsetShadow{
	drop-shadow:drop-shadow(0px 0 #000000 0.4 4 4 1 2 true);/*Used for TabBar*/
}
```
In swift this is then translated to:  
```swift
var distance:CGFloat = 0/*the distance a shadow casts itself*/
var angle:CGFloat = 0/*angle in degrees (-360 to 360) is used instead of as I think its more intuitive than figuring out inverse y values etc*/
var color:UInt = 0x000000/*the color of the shadow*/
var alpha:CGFloat = 0.4/*the opacity of the shadow*/
var blurX:CGFloat = 4/*the max value of blurX and BlurY will be used as a single blur value*/
var blurY:CGFloat = 4/*Same as blurY*/
var strength:CGFloat = 1/*This could be implemented in the future by adding many drop shadows on top of eachother*/
var quality:CGFloat = 2/*this could be implemented in the future and would work by 2x size resized to 1x etc*/
var inner:Bool = true/*if this is true the inner shadow scheme is employed to the cgcontext*/
```

- Inset shadow in quartz tutorial, (from PaintCode): [here](http://blog.helftone.com/demystifying-inner-shadows-in-quartz/)   
- This is a long discussion into inner shadows in quartz, the approach is a little different: [here](http://stackoverflow.com/questions/4431292/inner-shadow-effect-on-uiview-layer) 
- 2 shadows in one path: [here](http://stackoverflow.com/questions/8170678/ios-core-graphics-draw-only-shadows-of-a-cgpath)
- the problem with gradient and shadow, discussion here: [here](http://stackoverflow.com/questions/2577622/how-to-draw-a-drop-shadow-and-gradient-with-quartz2d) And [here](http://stackoverflow.com/questions/3160220/draw-a-rounded-uiview-with-gradient-and-drop-shadow) 

Non-inset-shadow: (DropShadow, also called outer shadow)  
<img width="120" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-12-08 at 05.35.28.png">


**Example exported from PaintCode:**
```swift
//// General Declarations
let context = NSGraphicsContext.currentContext()!.CGContext

//// Shadow Declarations
let shadow = NSShadow()
shadow.shadowColor = NSColor.blackColor()
shadow.shadowOffset = NSMakeSize(0.1, 0.1)
shadow.shadowBlurRadius = 10

//// Rectangle Drawing
let rectanglePath = NSBezierPath(rect: NSMakeRect(91, 38, 55, 56))
NSColor.grayColor().setFill()
rectanglePath.fill()

////// Rectangle Inner Shadow
NSGraphicsContext.saveGraphicsState()
NSRectClip(rectanglePath.bounds)
CGContextSetShadowWithColor(context, CGSizeZero, 0, nil)

CGContextSetAlpha(context, shadow.shadowColor!.alphaComponent)
CGContextBeginTransparencyLayer(context, nil)
let rectangleOpaqueShadow = NSShadow()
rectangleOpaqueShadow.shadowColor = shadow.shadowColor!.colorWithAlphaComponent(1)
rectangleOpaqueShadow.shadowOffset = shadow.shadowOffset
rectangleOpaqueShadow.shadowBlurRadius = shadow.shadowBlurRadius
rectangleOpaqueShadow.set()

CGContextSetBlendMode(context, kCGBlendModeSourceOut)
CGContextBeginTransparencyLayer(context, nil)

rectangleOpaqueShadow.shadowColor!.setFill()
rectanglePath.fill()

CGContextEndTransparencyLayer(context)
CGContextEndTransparencyLayer(context)
NSGraphicsContext.restoreGraphicsState()

```

**An example that adds outer shadow to the entire layer** (with customshadow path)
```swift
UIColor *color = [UIColor grayColor];
CGColorRef gray = color.CGColor;
self.layer.shadowColor = gray;
self.layer.shadowOffset = CGSizeMake(0, 0);
self.layer.shadowRadius = 2;
self.layer.shadowOpacity = 1;
self.layer.borderColor = red;
self.layer.shadowPath = [UIBezierPath bezierPathWithRect:self.bounds].CGPath;
self.layer.masksToBounds = NO;
```

**NOTE:**
Improving performance when using shadow: You can improve performance by using rasterization (which creates a cached bitmap of a CALayer). layer.shouldRasterize = YES; layer.rasterizationScale = [UIScreen mainScreen].scale;

**LINK**
This is an awesome description of how to make shadows from strokes w/ w/o transparency etc: [link](http://stackoverflow.com/questions/6709064/coregraphics-quartz-drawing-shadow-on-transparent-alpha-path) 

