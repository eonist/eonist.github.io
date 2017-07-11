Elements has CSS property links: Basically you replace property values with links. <!--more-->    
   
```swift
var str = "Button{fill:<ButtonBase>;} ButtonBase{fill:green;}"//And the fill of button would become green. 
```
  
Swift doesn't do RegExp.exec so there is no way to implement a direct string replacement routine that adapts as the string gets changed. 

**One way** to do it is to use swifts RegExp matches routine and then for loop backward over the matches. This way the changing length of the string wont matter because you are traversing backwards over it.  

**A second way** would be to offset the NSRanges given NSTextCheckingResult in the matches as you traverse over the string in the forward direction.