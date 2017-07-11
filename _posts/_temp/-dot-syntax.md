My notes on dot syntax in swift<!--more--> Dot syntax: 

You can achieve dot syntax with static var and extension: 

```swift
func test(_ color:NSColor){
    Swift.print("\(color)")
}
extension NSColor{
    static var blueColorVal:NSColor {return NSColor.blue}
}
test(.blueColorVal)
test(.init(r:1,g:0,b:0))//.init trick 👌
```

Or via enums of course. Example not needed