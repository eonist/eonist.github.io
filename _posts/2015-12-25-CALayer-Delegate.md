Since there is little information about this class on the internet I thought id write a small notice. <!--more-->  
1. set the layer.delegate = self  
2. override drawLayer(_ layer: CALayer, inContext ctx: CGContext) or displayLayer(layer: CALayer)) the later enables you to get the context in the same manner you would get the context within a drawRect call, the former allows you to easier get to the context, if you use the later the former wont be called.  
3. add all your drawing code inside the NSView drawLayer method.  

**NOTE:** You don't have to extend CALAyerDelegate, as it is just a formal interface. There are a couple of more methods you can use here: [link](https://developer.apple.com/library/mac/documentation/QuartzCore/Reference/CALayerDelegate_protocol/index.html#//apple_ref/occ/instm/NSObject/layoutSublayersOfLayer:) 

**NOTE:** The layoutSublayersOfLayer(_ layer: CALayer) method may come in handy if you have sublayers attached to the layer. 

**NOTE:**  
If you want to use a class that doesnt extend NSView as the delegation instance, Then you must extend NSObject because the CALayerDelegate is only able to call the methods if the respondToSelector method is implemented, which it is via NSObject. 
- [x] Find away around this. NSObject isnt a good solution (there seem to be non) there is one, ==utilize nsview as your nsobject and then pass ctx and layer on to a functional selector==. See the delegate without strong ref post about this. 
