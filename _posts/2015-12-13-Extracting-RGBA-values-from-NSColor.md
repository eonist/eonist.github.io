Extracting RGBA values from NSColor<!--more--> 
```swift
let nsColor:NSColor = NSColor.redColor()
let ciColor:CIColor = CIColor(color: nsColor)!
print(ciColor.red)//1.0
print(ciColor.green)//0.0
print(ciColor.blue)//0.0
print(ciColor.alpha)//1.0 /*or use nsColor.alphaComponent*/
```
**Extension for nsColor:**  
NSColor.redColor().rgba.r//Outputs 1.0

```swift
var rgba:(r:CGFloat,g:CGFloat,b:CGFloat,a:CGFloat){     
   let ciColor:CIColor = CIColor(color: self)!     
   return (ciColor.red,ciColor.green,ciColor.blue,ciColor.alpha)
}
```