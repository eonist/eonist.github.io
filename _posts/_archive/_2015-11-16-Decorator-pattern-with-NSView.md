Here is a possible solution<!--more--> :  
 
 - you use the decorator pattern
 - you build up your graphics directly to the context.
 - you dont draw inside the DrawRect call
 - you have a container class that hold the decorator structure. 
 - the container creates the decorator-structure inside its drawRect call (So that the CGContext is connected to this) and can be refreshed from here. 
 - you refresh the graphics by calling needDisplay on this Container
 - the decorator-pattern-structure base class does not need to be a view
 - you use transperancy-layer to clear graphic data you dont want to use. 
 - you start and end the transperancy-layer at the beginning and end of the decorator-structure
 - Each Decorator class has a clear method that calls CGContextClearRect() if you want to disregard previous drawn graphics in  
 
 - [x] Do a dummy test in a fresh ViewClass in XCode. Dont use previous code. 
 - [x] Test resizing the window this will refresh the procedure. 
   - [x] Try to also change a variable in the Decorator structure, like Color, use randomColor to see the effect
   
 If all this fails, then an idea is to use Protocol and Extension and just do Polymorphism that way. it will be alot of interfaces to cover all combinations, but you may figure something out.
 
 <img width="307" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-11-14 at 18.33.17.png">
