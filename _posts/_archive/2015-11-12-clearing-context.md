
### **An alternative "clear-procedure" by extending NSView**<!--more--> 
The problem with with using the TransparencyLayers to clear CGContext is that you need to know when to end the transparencyLayers. What if you wanted to move the graphic interactivly? once you end a TransparencyLayer it cant be cleared. So lets pursue another idea: The idea is to make a class named Shape which would extend FlippedView which extends NDView. The Shape class has a clear method that calls self.setNeedsDisplay(), which should call the drawRect method. You should try to figure out a way to only call drawRect once on init. Google it.

**Note:** Element also extends NSView but you shouldn't refresh this to clear, because you want to be able to manipulate the graphics inside an Element instance without calling drawRect in Element. Element resolves its graphic every time it is refreshed through drawRect so this wouldn't work. Think how the slider works etc. 


<img width="338" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-11-14 at 06.09.05.png">

## **A "clear-procedure" for CGContext**
1. Draw a red triangle
2. CGContextBeginTransparencyLayer(context,nil)
3. Draw a  blue circle
4. CGContextClearRect(context,bounds)//will now only clear graphics made after the CGContextBeginTransparencyLayer call was made
5. Draw a yellow square 
6. CGContextEndTransparencyLayer(context)

**Result:** You will now have a red triangle and a yellow square in the view (the blue circle was removed)  

**NOTE:** by using CGContextBeginTransparencyLayer and CGContextEndTransparencyLayer you avoid making a transparent hole that would extend through every graphic in the application context, which would be the case if you used CGContextClearRect without the TransparencyLayer procedure described above.

**NOTE:** Most likely all graphics drawn in the CGContext across all views are drawn together, not separate. This means that most likely there is no cost of extending NSView. So this may be a viable option if the clear procedure doesn't pan out. 

<img width="87" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-11-13 at 13.31.06.png">

## **Clearing Context**
When you want to clear context you can do this by:  
**1.** CGContextClearRect(context,NSRect(0,0,100,100)) this option will make a black hole in your context that sees through everything. So it has limited use.  
**2.** Redraw the NSView by calling setDisplay(), or some other method to call drawRect()  
**3.** Clipping?  
**4.** Go looking through books about this subject  
**5.** what about calling graphView.setNeedsDisplay() from The Graphic?  
**6.** you may try to use CGContextBeginTransparencyLayer / CGContextBeginTransparencyLayerWithRect and CGContextEndTransparencyLayer  

- [ ] Do more research around CGContext (google,stackoverflow)
- [ ] Verify that all NSViews use the same Context
  - [ ] either by finding the id 
  - [ ] or try clipping in two different elements (use playground)
- [x] Try the transparency Layers for grouping graphics and applying blend,shadow etc to them, this may work with the blend clearing technique you figured out but didnt work 100% (use playground)

```
/* 
   Begin a transparency layer in `context'. All subsequent drawing
   operations until a corresponding `CGContextEndTransparencyLayer' are
   composited into a fully transparent backdrop (which is treated as a
   separate destination buffer from the context). After the transparency
   layer is ended, the result is composited into the context using the
   global alpha and shadow state of the context. This operation respects the
   clipping region of the context. After a call to this function, all of the
   parameters in the graphics state remain unchanged with the exception of
   the following:
     - The global alpha is set to 1.
     - The shadow is turned off.
     - The blend mode is set to `kCGBlendModeNormal'.
   Ending the transparency layer restores these parameters to the values
   they had before `CGContextBeginTransparencyLayer' was called.
   Transparency layers may be nested. 
   */

```

<img width="331" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-11-13 at 08.08.57.png">
