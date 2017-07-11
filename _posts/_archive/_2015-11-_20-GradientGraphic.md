GradientGraphic now supports gradient rotation, albeit its not matrix transformation.<!--more-->  It works by finding the distance from the center of the paths bounding-box to one of its corners. It works for now, matrix transformation support in the gradient is already coded but is on hold until its needed.

**With correct rotation:**

<img width="339" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-11-17 at 12.20.06.png">

##Tests:

This css code: 
```
"Element{fill:linear-gradient(top,red,blue);}"
```
Produces this graphic: (mind the incorrect rotation)
<img width="320" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-11-16 at 14.46.01.png">


**StyleKit now has GradientLine**

The line-offset classes aren't working correctly yet. A unit test will sort it out. <!--more-->    

<img width="320" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-11-19 at 09.10.25.png">