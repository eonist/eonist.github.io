In order to support GradientLine it was necessary to go all the way down to the core , namely Quartz which is part of CoreGraphics. So currently implementing a routine to support GradientLine Directly.<!--more-->  
## Tests:

**Clickable Button with gradient and 2 states**  (Video):  
<a href="https://vimeo.com/144772908"><img width="345" alt="Vimeo video" src="https://raw.githubusercontent.com/stylekit/img/master/giphy23e23e.gif"></a> 

**Cap and Joint style support:**  
<img width="240" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/bevel234234.png">
<img width="240" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/round12493024.png"> 
 
**Radial gradient with Radial gradient line:**  
<img width="140" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-11-05 at 08.50.10.png">  

**Radial gradient:**   
<img width="140" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen%20Shot%202015-11-05%20at%2008.07.28.png">

**Multiple gradient stops** (with a custom automated stop creator):  
<img width="120" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen%20Shot%202015-11-05%20at%2006.58.46.png">

**Many shapes** implemented in the same context:  
<img width="180" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen%20Shot%202015-11-04%20at%2018.19.32.png">

**GradientLine** and **GradientFill**:  
<img width="120" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen%20Shot%202015-11-04%20at%2017.46.13.png">

GradientStroke:  
<img width="180" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen%20Shot%202015-11-04%20at%2017.31.46.png">

GradientFill and ColorStroke:  
<img width="130" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen%20Shot%202015-11-04%20at%2016.27.03.png">

## Notable findings:

This link has many cocoa examples (no gradient line)  
[link](http://www.knowstack.com/cocoa_drawing/) 

Pretty cool use of gradient in Cocoa:  

![example](http://lh4.ggpht.com/_gfktUGS0ov0/TUaT-TIUIbI/AAAAAAAAAqk/ulwdPURSfL4/iconapp.png?imgmax=800) 

[link](http://www.cocoawithlove.com/2011/01/advanced-drawing-using-appkit.html) 