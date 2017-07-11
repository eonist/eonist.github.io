Both are connected to drawing things in the CGContext of an NSView.<!--more-->   

**needsDisplay:Bool:** When you set/call needsDisplay to true, it is not a direct call but rather a request for osx to clear the CGContext and then call drawRect() 

**drawRect()** If you call drawRect() directly and you have your drawing code inside this method. then the drawing will be added on top of whatever is in CGContext at the moment. 

**NOTE:** why is apple making this process so complicated? I assume its because apple wants the actual drawing to happen when and how they want it to. 

**NOTE:** Also setting needsDisplay more than once doesnt result in many calls to drawRect(). Draw rect is only called once. unless you call this variable from a user interaction at which point drawRect() is called. (Verify this threory)
