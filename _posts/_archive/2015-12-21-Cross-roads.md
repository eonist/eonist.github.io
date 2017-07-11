NSView & CALayer combo vs Root NSView w/ CALayer <!--more--> 

**"NSView & CALayer combo"**  
**Pro:**  
- ==Support for NSTextView== (very important since NSTextView is something you cant implement your self)  
- No need for a root view that extends an interactivity-manager,NSView children manage their own up,down,over,out,moved.  
- Cognitively easier to grasp the framework  

**Con:**  
- You have to manually manage offsetting the frame of your "Visual" children (which sometimes you cant with moving objs etc)  , **what if a subchild has a stroke that extends beyond the frame of the NSView parent etc.** ~~(trouble)~~  ==Update 1: its a bit more code but you can offset the frame and add space etc. it workes==
- The only way to support borders would be to have all borders draw inside the frame. NO support for center. and outside would actually hide borders. ==Update: you offset and add space. it works==
- It will be trickier to implement animation. ==Update 1: it seems easy enough in the init testings of anim==
- Tricker to implement drag and drop inside a window (drag and drop between windows is as easy/hard)
  

**"Root NSView w/ CALayer"**   
**Pro:**  
- You don't have to manually manage setting the frame of your "Visual" children (which sometimes you cant with moving objs etc)  
- It will be easier to implement animation  
- Could be faster  
- Easier to port to IOS (IOS uses CALayer)  

**Con:**  
- You will have to manage the interactivity from the root NSView. (passing down,up,over,moved etc down to the up-most visual child)  
- You will have to manage the hit-area of the "visual" children by using the built in isPointWithin(rect/path/circle/ellipse/etc)  
- Root NSView will need to extend a form of Interactivity manager.  
- It will be harder to implement the full features NSTextView (this will need to be manually implemented in the root view etc etc)  
- you have to use layer.addSubLayer(Element()) or implement an add(Element()) that would do the same. Its both slightly cognitively harder to grasp  

**Todo:**  
- [x] Make sure you got layer hosted vs layer backed right. That both doesn't work w / o clipping
- [x] test the CATextField with a few of the most important Text API calls
- [ ] try pinch to zoom, and other gestures (must work on a CALayer aswell)
- [ ] play around with the catextlayer and see what you can do. If this works its major anyway. (Apple docs has a ref to a way to geometricaly measure core text etc)
- [x] **CATextLayer cant be selected, CALayer has no Interactivity, So look for ways to add functionality to CATextLayer** (TWUI has an example)
- [ ] create a test case for the "Root NSView w/ CALayer" scheme
  - [ ] Test the button interactivity cases. 
    - [ ] over,out,down,up,upinside,upoutside,moved
    - [ ] take a look at how slider is implemented in the old Element project
    - [ ] make an example where you add an event-listener to the window interactivity manager. 
      - [ ] Can you get to the window from a CALayer?
    - [ ] Make sure you can disable a CALayer so that the CALayer under it actually gets the mouse action (this is required to get drag and drop working)

NOTE:
It seems if you set wantsLayerUpdate to false, you can have a NSView and a CALayer in the same root NSView, this isnt revolutionary, but it may allow for NSText to be implemented in the future . no within the CALAyer hierarchy but in the root NSView at least.

**SIDE-NOTE:**    
There is a third option: Going back to using drawRect with the sibling overlapping problem. But then experimenting with different ways one could seperate the calculation of the graphics and the actual drawing, the drawing would have no calculation and the calculation would only happen if the graphic needed an update. THen you could setup a way for the siblings to notify its parent that would then drawRect both siblings in the order they were first drawn. 

## **NSView and CALayer combo**
NSView's for interactivity and CALayers for displaying graphics. CALAyers doesn't have interactivity (this can be injected through a root NSView, but this seems hacky) So the best approach seems to combine both. This also solve the sibling overlapping problem, but poses a problem with clipping the frame. This can be mitigated by separating the stroking and filling. This will now be done in the BaseGraphic class. 

**Why are we doing it this way?** Why not use layer.fill and layer.line? Because Layer doesnt support Gradient as is, and CAGradientLayer doesnt support Radial gradient. And Quartz doesn't support gradient lines at all. This needs to be faked. This is why we use the "NSView & CALayer combo"

Examples will come. 