The CSS transform param is now supported in Element.  <!--more--> 

<img width="172" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/css_transform.mov.gif">


```swift
var css = "Button{"
css += "fill:blue,~/Desktop/ElCapitan/svg/arrow_right.svg grey6;"
css += "transform:rotate(0deg),rotate(0deg);"
css += "}"
css += "Button:over{"
css += "transform:rotate(0deg),rotate(90deg);"
css += "}"
StyleManager.addStyle(css)
let btn = addSubView(Button(100,100,self))
_ = btn
```

**Only supports rotation for now**  

But the rest of the transforms are trivial to implement, even Transform3D 😻