A graphics framework built on Quartz <!--more--> and core graphics in swift  

<img width="650" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-12-26 at 10.30.58.png">

The swift code for the above example:  

```swift
/*Gradients*/
let gradient = LinearGradient(Gradients.red(),[],π/2)
let lineGradient = LinearGradient(Gradients.teal(0.5),[],π/2)
/*Styles*/
let fill:GradientFillStyle = GradientFillStyle(gradient);
let lineStyle = LineStyle(20,NSColorParser.nsColor(Colors.green()).alpha(0.5),CGLineCap.Round)
let line = GradientLineStyle(lineGradient,lineStyle)
/*Rect*/
let rect = RectGraphic(40,40,200,200,fill,line)
addSubview(rect.graphic)
rect.draw()
/*Ellipse*/
let ellipse = EllipseGraphic(300,40,200,200,fill.mix(Gradients.teal()),line.mix(Gradients.blue(0.5)))
addSubview(ellipse.graphic)
ellipse.draw()
/*RoundRect*/
let roundRect = RoundRectGraphic(40,300,200,200,Fillet(50),fill.mix(Gradients.orange()),line.mix(Gradients.yellow(0.5)))
addSubview(roundRect.graphic)
roundRect.draw()
/*Line*/
let lineGraphic = LineGraphic(CGPoint(300,300),CGPoint(500,500),line.mix(Gradients.deepPurple()))
addSubview(lineGraphic.graphic)
lineGraphic.draw()
```

The graphics framework is open source and can be found on github [here](https://github.com/eonist/swift-utils)   

## **Features:**
- Supports Rect,RoundRect,Ellipse,Line
- Built on Quartz and Core Graphics. 
- ==Supports animation== (Core animation layers)
- Supports Linear and Radial Gradient Stroke (This isn't supported in Paint code for instance)
- Supports Linear and Radial Gradient Fill
- Supports outside inside and centered strokes (Uniform center align coming soon)
- Easy to extend through subclassing or decoration. (Extensions should be used for custom initializers)
- Supports custom paths
- 2-layer system for stroke and fill (can be updated individually)
- ==inner-shadow==
- outer-shadow
- Built with a decorator design pattern (Think Matryoshka doll) 
- ==Performant drawing directly to the graphics card== for cached reuse while animating etc
- Draws directly to the CALayer CGContext 

## **Features coming soon:**
- Path Decorator (closed paths)
- ==SVG Asset Decorator== (closed paths)
- Blend-modes
- ==Axial gradient== (think spread out fan)
- Multiple point gradients (maybe if its applicable)

**NOTE:**  
The system uses an NSView as the container for the CALayer that contains 2 CALayers (Fill and Stroke) The system is Layer-hosted so that the subchildren isnt clipped by the parent frame etc. Its done this way to fully support NSText etc. Its possible to not use NSView by creating a new Graphic class that extends CALayer instead. The benefit would be performance the drawback would be that you would need to add the layer to a Root NSView. This could be a good solution where performance is key, like where there is a lot of animation, or many items. 

**NOTE:**  
The actual drawing is done in a Custom Shape layer that has a Graphics class instance. This Graphics instance does all the ==drawing to CGContext==. 

## **Tasks:**  
- [ ] What if you made a graphic framework that has a param called uniform. Which toggles how the graphic is aligned. Uniform means that outlines go outside the 0,0 coordinate system. and if uniform is false then its drawn from the 0,0 coordinate system
- [x] Find that medium article on axial gradients
- [ ] Find the research on multiple point gradients. There is that Russian guy that did this somewhere in an online help doc.  
- [ ] make the GraphicLayer class that can be used when using NSLayer isn't performant enough.
- [ ] make a nice test Platform for graphics, use dashed grid, and top and left rulers with numeric height and with. This is useful for showcasing the power of StyleKit graphics engine and for future tests. Also gives great contextual value. 
- [ ] Try making the PathGraphic with many paths closed and open. We need this for the grid and rulers
- [ ] research file loading in swift both external and internal
- [ ] research gestures, add a zooming test like you did before. 
- [ ] add many SVG shapes (think Karim Rashid)
- [x] support gradient line and color fill and vis versa


**NOTE:** 
An idea is to separate out the Graphics part of StyleKit to another repo named: GraphicsKit. [^1]

---

[^1]: And also seperate out SVGKit or SVGLib, depending on the availability
