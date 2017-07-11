Turns out that in the original concept of Element, the borders aren't drawn outside the frame of the Element. Its drawn inside, and then the fill is offset in order to support this. <!--more-->  (the concept of outside border vs inside border still applies, inside means the side that is inside is also hidden) If you think about it, it actually makes more sense. since you want a button to be exactly 100x20. not 100x20 + or - border width + or - margins etc. If you want a button that is 100x20 w/ a 2px border you simply make the size: 96x16. this way everything fits snuggly inside a 100x20 block. And also if a border was drawn outside of the actual frame then you couldn't set a button at x:0 it would then be hidden outside of the window. 

This is great news because it will allow for the framework to use an NSView hierarchy that is layer backed. Opposed to a layer-hosted approach. (Layer-hosted wouldn't let children add NSViews) 

The only drawback I can think of is that you have to make sure that "Container views" needs to allocate enough width and height for its children to be drawn in. You cant simply add a container and have it automatically not clip. 

Why is using layer-backed views so important? Because you need NSLayer, and handling interactivity on each individual view seems like a better approach. Also utilizing the rich NSTextField class simplify things a lot. 

**NOTE:**
A way to avoid having the Skin class editing the size of the element class (major design flaw) is to always draw the border of an Element inside the frame of the Element. The only difference would be that you would have to add the thickness of the border to your button width if you really needed the fill to be the exactly say 100px. then the size would be 104px if the border thickness is 2px. (This isn't exactly "the css box model way" of doing things) **It would keep the project moving** and implementing "the pure Core Animation approach" wouldn't be that much work later if it is needed. One could argue that the way the css box model works its easier to embed an image and have the image have a border around it self, or similarly with an SVG asset. However you could still do this by using the skin layers. then either set a padding or an individual offset on this skin layer. 

**NOTE:**
When using RoundRect fillet and combined with a stroke, the line that makes up the stroke needs to be adjusted to fit snuggly with the round rect. 

**NOTE**
When you have time, try to combine the 3 links mentioned in this post and make an effort to support Core Animation. It could be really great. Keep in mind that you will have to solve the interactivity problem and setup the outer shadow to be applied to the layer instead of drawn using Quartz. These problems seem solvable though. The custom "selectable & editable" CATextLayer seems more difficult. And is out of scope for now.

- [x] Take a look around and see if this can be a potential problem before you settle on this solution.  
- [x] Take a look at the old notes about the box model

**Note:**  
Remove the notion of center line-offset. as it is not used in the old Element concept  

**NOTE:**
You cant add a dropshadow on a layer-backed view and have it be visible outside of its frame. However this could be implemented either via the framework (~~offseting the frame etc~~) or manually by adding some margin or padding to achieve the same result. The later would be preferred since it makes the framework easier to grasp. And the cost of manually being aware that you need to add some padding for the dropshadow to be visible, isn't that big of a deal. (same with the focus ring) Moving on!

- [ ] **Start implementing the new border paradigm (that is closer to the original concept), test it with a basic element then make a button etc**
- [x] Figure out how you archived dropshadow before. You need to be able to draw a dropshadow on layer-backed views without resorting to padding and margin etc. If this doesn't work, then we are back to the pure Core Animation approach again. 
- [x] Make a project search in atom first to see if anything uses it. doubtfully
- [x] Research if you can draw a shadow on a layer. (this could work great as a focus ring) (you cant)
- [x] You could test if you can add a layer-backed view to a View that doesn't clip its frame. If this works it could enable you to have more freedom. (This didn't work, as soon as you add a layer-backed view, the entire hierarchy starts to adhere to clipping fo the frame)
- [x] Also do more research into bound vs frame as this seems to be unclear at present time of writing. 

**Note:** TWUI has a class that enables CaTextLayer to be edited and selected: [link](https://github.com/twitter/twui/blob/49d3062006e0fb4d6e904dbcebfb4ffdfb629f21/lib/UIKit/TUITextEditor.m) 

**NOTE:**  
Also apple has some information on how one could implement text selection and editing etc: (this may come in handy if you choose to implement your own textView that has selection and editing capabilities) [link](https://developer.apple.com/library/ios/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/LowerLevelText-HandlingTechnologies/LowerLevelText-HandlingTechnologies.html) 

TWUI uses CALayer for everything, the drawback with this approach is that you also get a lot of overhead. You have to manually implement as it seems a complex custom CATextLayer subclass, in order to be able to edit any text etc. 
This could revive the CALayer approach on a later date though. If it is needed for animation etc. or there is a roadblock in the layer backed approach.

**Resources:**
A discussion on Core Text customization in IOS: [here](https://www.cocoanetics.com/2011/01/befriending-core-text/)   

**Conclusion:** 
So going forward we will go with the Layer-backed scheme. However this may be changed in the future, if Core Animation is needed for animation purposes (this requires an effort to make an editable CATextLayer, think TWUI) But it probably will be alot faster aswell something like the list view may actually require this. So go with plan A but revert to plan B if plan A fails.