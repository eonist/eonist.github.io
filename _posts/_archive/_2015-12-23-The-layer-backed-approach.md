What if Skin extended CALayer, and each decoratable also was a CALayer. As skin has no benefit of being an NSView, except for the TextSkin. <!--more-->  So no go. Keep skin as an NSView that is layer-backed with many decoratables with 2 shape composited. FillShape and LineShape

**NOTE:**   
Theoretically Skin could be a CGContext drawer not extending anything. (as it was setup in the begining), although CALayers have the added benefit that they are cached to the graphics card individually. And if you update one layer, then the other layers doesn't need to updating etc.

**NOTE:**  
Use the layout method in layer-backed view to setup the text, and then you call needsLayoutUpdate when you call the setText() method, this way you don't update the graphics behind etc. 

**NOTE:**  
an upside by going with the layer backed approach is that it will be way easier to ==port Element to IOS as well==, since you then can utilize the TextField class in IOS. and not have to worry about implementing ca-texx-tlayer for ios. (unless ios has a great calayertextfield)

**NOTE:**  
With layer-backed views you will have to add margin in order for outer shadows to show. its a drawback that layerhosted views doesn't have. 

**NOTE**   
Unlike the original Element concept, the css-box-model is a little different in that everything needs to fit inside the width and the height you set to an Element. This has its negatives and its positives. A positive is that the floating algorithm becomes much easier to implement. ==as you can rely on the with and the height by it self==, At the same time it makes the drawing code ==more complex== since you need to account for offsetting the when fitting things inside the frame.  

**NOTE**  
You have to call needsDisplay to update the graphic and then needsLayout to update the TextField

**NOTE**
padding and margin needs to be applied to the frame of the skin. skin can be a CALayer but then textskin needs to be an NSView and you will need to create two base skin classes.

## Tasks:

- [x] Watch the entire layer-backed view video again (so that you pick up any thing you have missed)
- [ ] Review all the notes on layer-backed views
- [ ] also study the core animation book
- [ ] ==study the drawkit code, does he use calayers?==
- [x] test how padding and margin works with the old element
- [x] Start implementing the new box model
