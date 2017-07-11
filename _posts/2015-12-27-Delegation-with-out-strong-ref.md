It's possible to implement a delegation without resorting to a strong ref. <!--more-->

**NOTE:** The basic concept is that you forward the delegate call to a selector call

1. Create a selector instance in the NSView you want to get the delegation from  
2. implement the drawLayer(layer,ctx) in the NSView you want to get the delegation from call the selector variable with the layer and ctx vars  
3. set the view.selector to a handleSelector method where you then retrieve the layer and ctx (this can be anywhere in your code, weak or strongly referenced)

To see an example of how you implement the selector construction:(Permalink) https://github.com/eonist/Element/wiki/Progress#selectors-in-swift  
TO re-delegate a delegate to avoid extending NSObject idea. with insp from the twitter article you made about button delegation etc  
    
1. what you do is that you setup Graphic to be the delegatee of the fillShape and the lineShape. so ==whenever they get a setNeedsDisplay call then this is always the delegate method being called==  
2. then in the drawLayer(context,layer) method in graphic, you set the ctx and layer to the selector variable, ==then whatever is in the selector gets the call==,   
3. this way you can have the decorator class get the delegate call with out the messy extending of NSObject etc.  
- [x] TODO:Test your selector theory first in a local variable as this is the main benefit of this approach, if it could fix this part. (This works)
- [x] Try to implement the idea

**NOTE:** why are we doing this? because ==creating a variable outside methods whenever you want to use the Graphic class is non-sensical==  
**NOTE:** And you also get the benefit that the receiver of the delegation doesn't need to extend NSView or NSObject  
