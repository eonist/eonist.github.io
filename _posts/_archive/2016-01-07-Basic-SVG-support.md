SVG support is coming along. <!--more-->

<img width="314" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-17 at 10.31.13.png">

<img width="609" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-17 at 19.45.23.png">

<img width="469" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-19 at 16.47.01.png">

### Gradient engine
Scaling the gradient could in-fact be the job of the caller after all, since the boundingbox isn't always the reference to work on, think what happens when you manipulate individual path points as oppose to scaling the entire shape at which point you should ==use the new scale and append this to the transform matrix==, rather than calculating the new boundingbox each time, which can be ==cpu intensive==. The only thing is that the relative values provide convenience that enables you to get quick results. How is this handled in the old code? I presume with a matrix? 

Maybe you should use a ==dual system with absolute and relative values==, and when you are using relative values you recalculate the boundingbox each time. This way its easy to use and optimized when you need it to be, the useRelativeValues should be set to true by default!

The solution is of course to let Graphic handle relative values,and graphics handle absolute values. Just handle. The cgfloat differently as scalar in graphic and normal absolute values in graphics. 

Look over the frame and offset code first. There may be things you haven't considered. 

If you later down the line need to support absolute values in the graphic class as well then you just implement a bool val to reflect this change. 

**Discussion:**  
Illustrator doesn't anchor the gradient, but rather stretches the gradient to fit the boundingbox. even when manipulating path points. This doesn't mean that you need to follow this unexpected convention. 

**Discussion:**  
Actually you need Different systems. For Graphic you need to be able to use Rotation for linear and focal ratio and rotation for radial. Then you Make a class named GraphicsGradient and GraphicsLinearGradient and GraphicsRadialGradient and IGraphicsGradient, these classes will have absolute values and only work on the Graphics class. In the end you will have 3 Gradient class categories. ==SVGGradient==, ==Gradient== and ==GraphicsGradient==. CSS will use the Gradient class. SVG will use the SVGGradient and the graphic framework will use Gradient as well. All systems will eventually use the GraphicsGradient in Graphics which is based on absolute values. This way you can add features to the Gradient and SVGGradient classes if its needed at a later state. The underlaying GraphicsGradient can stay the same and ==wont break the classes that uses it==. 

<img width="639" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-12 at 14.33.57.png">

- [x] SVGPolygon with color fill support
- [x] SVGPolygon with color line support
- [x] SVGRect support
- [x] SVGCircle support
- [x] SVGEllipse support
- [x] SVGStyle.stroke color support [Miter limit research]
- [x] Fix the frame offset problem with SVGPolygon
- [x] start using ios9 colors for tests, grab them in the dropbox folder.
- [x] SVGLine
- [x] SVGPolyLine
- [x] ~~SVGPathGraphic~~
- [x] ==SVGPath (This involves bezier path conversion etc, will be a challenge)== [Apple bezier resources]
- [x] Try many shapes in one SVG file
- [x] Try the search icon (it makes use of winding etc)
- [x] Support matrix transformations applied to a linear Gradient
- [x] Support scaling a shape with a linear gradient
- [x] Try the gradient stroke (linear) ref this link [Gradient stroke]
- [x] Separate Gradient into two subclasses, LinearGradient and RadialGradient.
- [x] Try the gradient fill (linear)
- [x] Try the gradient stroke (linear)
- [x] Try the gradient fill (radial)
- [x] Try the gradient stroke (radial)
- [x] Implement support for percentage "%" values for fill in SVGGradient (relative values)
- [x] Implement support Relative "%" values for stroke (requires the PathOutline)
- [x] make sure gradient stroke works in the Graphic framework (linear and radial)
- [x] Implement an universal Gradient engine that is both relative and absolute.
- [x] Try scaling an SVG instance with the SVGModifier.scale method
- [x] Test exporting SVG from illustrator and opening in StyleKit. Does it look correct?
- [x] Test all possible stroke, non-stroke, fill, non-fill combinations
- [x] ==Add SVGAssetGraphic== 
- [x] Implement RoundRect, add roundrect primitve to test with
- [ ] Test many matrix transform cases
- [ ] Implement matrix transform for relative values as well (not high priority)
- [ ] Test arc paths
- [ ] Make sure all SVG primitives are created by making an universal test to test them all. 
- [ ] make the old OSX close minimize and maximize icons work, 
- [ ] Download and check the [Sketch vector resources] osx icons. 
- [ ] then find and make the new ones [Sketch vector resources] and [Sketch vector resources 2] and [Sketch vector resources 3] and love this one [Sketch vector resources 4]
- [ ] SVGShadow support like this: [SVG shadow]
- [ ] Test the support for extracting SVG syntax from view based graphics. So that the export will work
- [ ] All SVG types should be exportable
- [ ] Add support for 0.5px strokes (retina) this may require detecting the users screen res etc.
- [ ] Write examples of how to use this SVG lib in the swift-utils class
- [ ] If there is a stroke color the width should be drawn at 1px, but the SVGStyle should still have nil as its stroke-width
- [x] Make a test case similar to the SVG class and try to print its variables and its children's variables. something is wrong.
- [x] Research when to use weak and when to use strong variables this may be the cause of why you cant parse through the svg class [weak vs strong](http://krakendev.io/blog/weak-and-unowned-references-in-swift)  and [here](https://www.andrewcbancroft.com/2015/05/08/strong-weak-and-unowned-sorting-out-arc-and-swift/) 
- [ ] when you scale the lineGradient, remember to consider that you don't scale the lineWidth and so the gradient that is applied may not cover as it should. This means that you cant simply scale the SVGGradient.transformation, instead you have to scale the gradient that is attached to the individual SVGGraphic, and you have to take into consideration that the strokeThickness is now different than the new relative size. ==Even though its the same, the relative difference between the strokeThickness and the size of the shape is different.== So you have to recalculate the gradient.transformation matrix a little bit. Probably by insetting it by some relative value
- [ ] _Figure out the difference between CGPathGetPathBoundingBox, CGPathGetBoundingBox_
- [ ] Axial Gradient also called [Canonical gradient]
- [ ] Compact the Gradient classes. shorthand ifs and group similar utility code, remove comments
- [x] ~~the reference issues you are struggling with is probably due to the fact that your casting instances and while you do this you lose the reference to the original instance, Maybe (It could be that you need to use the inout param, but why then doesn't it work on simple describe methods?)~~ (it was a lazy variable)
- [ ] check if you support inline style like this: style="fill:red;stroke:black;stroke-width:5;opacity:0.5"

<img width="499" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-18 at 09.28.18.png">

<img width="661" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-16 at 17.05.07.png">

<img width="675" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-16 at 15.09.12.png">

**NOTE:**  
==Never use lazy variables again==, they will make you work for 6 hours straight trying to figure out a reference bug. They are not worth it, just instantiate the variable on init. Making it lazy wont save you a lot of memory, if you really want lazy add it after you have completed the application as optimization.

**DISCUSSION:** 
- you need to resolve the issue with GradientGraphic not being positionable. Consider the implications for LineGraphic, EllipseGraphic, RectGraphic, SVGAssetGraphic when you implement this, the problem is probably that you wrap gradientgraphic around rectgraphic, could it be done the other way a round and then you would have access to an ipositionable? You can revisit this problem later if this solution works, its not difficult to implement theipositionable to the gradientgraphic, you could in theory even add a getter for access to the first available ipositionable, although then you risk not calling the parent wrapper, which shouldn't matter anyway since gradientgraphic shouldn't care about what size or position the underlying graphic is. If you need the gradient to scale then this should be set by the gradient graphic on init, as relative values. Absolute values shouldn't scale. You shouldnt use absolute values anyway. 

- so this means that you actually can calculate the gradient box on gradientgraphic draw call from user, and use these values as relative percentage values for the graphics class. You then update the path and not the gradientgraphic and the gradient will follow the relative values with the boundingbox of the path.

- these relative values will also work for viewport values. So you dont have to impliment this in the graphics class.

- the absolute values should be supported in the graphics class, enable them with a bool var.

- wait, what about the frame offset etc? You dont have to worrie about that, you are working with relative values, and in the graphics class you work with the boundingbox of the path.

- what about he gradient box, well then you access the path via graphic.path. Which will be avilable since you call draw first.

- Gradient ahould also be split into two classes to derive its grradient type, and have no rot,focal point etc. focal point is done when you parse the css in cssstylepropertyparser it is then converted to a relative 2 point, 2 radius radial gradient which will scale with the min radius of the bounding box, the constraint here should be similar to illustrator, todo: confirm this with a test
        
- When a radial gradient is set it will follow the shape of the boundingbox when its resized. If you want a different behaviour you will have to update this from the GradientGraphics point of view.
        
- you should probably support absolute variables aswell not just % THink SVG and how we do it there

<img width="465" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-14 at 16.54.24.png">

<img width="491" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-14 at 16.52.20.png">

<img width="352" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-14 at 08.36.14.png">

<img width="154" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/linear-gradient_mozilla cc_3132.png">

Use these ==IOS9 colors== when debugging:  
<img width="420" alt="img" src="https://dl.dropboxusercontent.com/u/2559476/92402Colors.jpg">   

**Hex colors:**  

```
light blue #5AC8FA
medium blue #007AFF
yellow #FFCC00
orange #FF9500
green #4CD964
red #FF3B30
magenta #FF2D55
gray #8E8E93
light gray #EFEFF4
medium gray #CECED2
black #000000
```
More debug colors:
<img width="600" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/342423ColorsPalette.png">

```swift
public struct ColorCodes{
    public static let turquoise = "1abc9c"
    public static let greenSea  = "16a085"
    public static let mediumTurquoise = "4ECDC4"
    public static let lightSeaGreen = "1BA39C"
    public static let emerald  = "2ecc71"
    public static let nephritis = "27ae60"
    public static let gossip = "87D37C"
    public static let salem = "1E824C"
    public static let peterRiver = "3498D8"
    public static let belizeHole = "2980b9"
    public static let riptide = "86E2D5"
    public static let dodgerBlue = "19B5FE"
    public static let amethyst = "9b59b6"
    public static let wisteria = "8e44ad"
    public static let lightWisteria = "BE90D4"
    public static let plum = "913D88"
    public static let wetAsphalt = "34495e"
    public static let midnightBlue = "2C3E50"
    public static let hoki = "67809F"
    public static let ebonyClay = "22313F"
    public static let sunflower = "F1C40F"
    public static let tangerine = "F39C12"
    public static let confetti = "E9D460"
    public static let capeHoney = "FDE3A7"
    public static let carrot = "E67E22"
    public static let pumpkin = "D35400"
    public static let ecstasy = "F9690E"
    public static let jaffa = "F27935"
    public static let alizarin = "E74C3C"
    public static let pomegranate = "C0392B"
    public static let monza = "CF000F"
    public static let thunderbird = "D91E18"
    public static let clouds = "ECF0F1"
    public static let silver = "BDC3C7"
    public static let gallery = "EEEEEE"
    public static let iron = "DADFE1"
    public static let concrete = "95A5A6"
    public static let asbestos = "7F8C8D"
    public static let pumice = "D2D7D3"
    public static let lynch = "6C7A89"
}
```

<img width="300" alt="img" src="https://www.dropbox.com/s/b5h6e77u8de93zh/Screen%20Shot%202016-01-01%20at%2018.51.56.png?raw=1">  


**Radial gradient without background:**  
<img width="295" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-17 at 10.29.57.png">
**Radial gradient with knockout mask:**(The path of the mask is the radial gradient area knocked out of the path of the graphic)  
<img width="307" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-17 at 10.30.20.png">

This code was added as a way to make a knockout background for the radial gradient. However this was later accomplished through the use of extending the radial gradient after end. The path is used as a mask anyway so this is a really good solution. And its faster. But the bellow code can come in handy if you want to achieve EvenOdd clipping/masking at a later point. Or if the current solution has problems.  
 
```swift
/*begin create background*/
CGContextSaveGState(context)/*store the graphic state so that the mask call bellow doesnt become the permanant mask*/
CGContextAddPath(context,path)/*Add a path as the background*/
var ellipsePath:CGMutablePath = CGPathParser.ellipse(gradient.endCenter, CGSize(gradient.endRadius*2,gradient.endRadius*2))//base this on the data from the radial gradient
if(gradient.transformation != nil) {ellipsePath = CGPathModifier.transform(ellipsePath, gradient.transformation!)}/*we check if there is a transformation applied to the gradient before we use it*/
CGContextAddPath(context,ellipsePath)/*the ellipsePath represents the shape of the gradient, the shape of a radial gradient can infact be another shape aswell but they way we use it it will always be an ellipse, to make support for the other shape you would have to make a shape out of two elliptical shapes and then merge them together with tangents touching both ellipses, you have code for this if its needed*/
CGContextEOClip(context);/*using clip here may break the gradient stroke clipping, it may also be less optimized than just drawing a square with a hole in it by paths that use winding*/
CGContextAddPath(context,path)/*we need to have a path to fill something in again, since the clip consumes the clip path etc*/
CGContextSetFillColorWithColor(context,gradient.colors[gradient.colors.count-1])/*Sets the background to the same color as the first gradient color, this is needed to fill the entire path*/
CGContextDrawPath(context, CGPathDrawingMode.Fill)/*draws the background color to the context*/
CGContextRestoreGState(context)//restore the graphic mask
/*end create background*/
```

Solved the knockout problem: (these are the tests)  
<img width="369" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-19 at 15.46.09.png">
<img width="399" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-19 at 15.50.05.png">
<img width="417" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-19 at 15.52.14.png">
<img width="435" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-19 at 15.52.34.png">
<img width="415" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-19 at 15.53.18.png">
<img width="499" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-18 at 09.28.18.png">



<!--more--> 
[Gradient stroke]: http://vanseodesign.com/web-design/svg-linear-gradients/
[Miter limit research]: https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/stroke-miterlimit
[Sketch vector resources]: http://www.sketchappsources.com/tag/osx.html
[Sketch vector resources 2]: http://yosemiteui.com
[Sketch vector resources 3]: http://sketchrepo.com/free-sketch/tvos-ui-kit-freebie/
[Sketch vector resources 4]: http://sketchrepo.com/free-sketch/mail-app-concept-freebie/

[Apple bezier resources]: https://developer.apple.com/library/mac/documentation/GraphicsImaging/Reference/CGPath/#//apple_ref/c/func/CGPathAddCurveToPoint
[SVG shadow]: http://www.svgbasics.com/filters3.html

[Canonical gradient]: http://jsfiddle.net/Weytu/ 
