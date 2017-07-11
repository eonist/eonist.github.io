The specificity engine supports 4 kinds of selector types: <!--more-->   
1. Element type selector: "Button"  
2. ID selector: "#id"  
3. class selectors: ".class-id"  
4. State selector: ":state"  

```swift
func styleTesting(){
   var css = ""
   css += "Element#first{fill:red;corner-radius-top-left:40px;}"
   css += "Element#second{fill:blue;corner-radius-bottom-left:40px;}"
   css += "Element#third{fill:yellow;corner-radius-top-right:40px;}"
   css += "Element#fourth{fill:green;corner-radius-bottom-right:40px;}"
   StyleManager.addStyle(css)
   self.addSubview(Element(150,150,nil,"first"))
   self.addSubview(Element(150,150,0,150,nil,"second"))
   self.addSubview(Element(150,150,150,0,nil,"third"))
   self.addSubview(Element(150,150,150,150,nil,"fourth"))
}
```
<img width="412" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-12-03 at 19.15.15.png">
