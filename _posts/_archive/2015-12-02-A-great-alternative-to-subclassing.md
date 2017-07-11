### This is a great way to add functionality to existing classes and new classes by having a common extension to a common protocol.  <!--more--> 
Adding a protocol to NSButton: 

```swift
protocol IButton{
    //func someMethod()
}
extension NSButton:IButton{
    public override func drawRect(dirtyRect: NSRect) {
        someMethod()
    }
}
extension IButton{
    func someMethod() {
        Swift.print("works")
    }
}
let btn = NSButton(frame: NSRect(x: 10, y: 10, width: 100, height: 100))//output: works
/*Ways to call the new method:*/
btn.someMethod()//output: works
(btn as IButton).someMethod()//output: works
func test(button:IButton){
    button.someMethod()
}
test(btn)//output: works
```

### Note:
There is also swiveling of methods. Where you can sort of inject code into Classes that may be hard to extend or add functionality on. It has cavities though so do a lot of research. 

