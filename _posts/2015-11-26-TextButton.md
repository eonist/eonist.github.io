Testing some states with some different styles<!--more--> :  

**CSS:**
```
TextButton{
   fill:red;
}TextButton:over{
   fill:yellow;
}TextButton:down{
   fill:green;
}Text{
   font:Lucida Grande;
   selectable:false;
   size:22px;
   color:blue;
   align:center;
   backgroundColor:orange;
   background:false;
}Text:down{
   color:white;
}
```
**Swift:**
```swift
StyleManager.addStyle(css)
let textButton = TextButton("Hello world",200,80)
self.addSubview(textButton)
```
<img width="320" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/23oifje2424.gif">
