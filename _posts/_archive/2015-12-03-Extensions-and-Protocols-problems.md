<!--more--> 
```swift
//a variable that is a base varaible
protocol IDecoratable{}
class A:IDecoratable{}
//var a:IDecoratable = A()
//a variable that extends the base variable and has a protocol
protocol IPositional{
    var position:CGPoint{get set}
    func setPosition(position:CGPoint)
}
extension IPositional{
    mutating func setPos(pos:CGPoint){
        position = pos
    }
}
class B:IDecoratable,IPositional{
    var position:CGPoint = CGPoint(x: 0, y: 0)
    func setPosition(position:CGPoint){
        print(position)
    }
}
let a:Any = B()
//try to cast the first variable to the second as its protol 
//then try to use the setMethod() of the protocol which is confroming to the second variable
(a as! IPositional).setPosition(CGPoint(x: 2, y: 2))//this compiles
(a as! IPositional).setPos(CGPoint(x: 4, y: 4))//this doesnt compile because it uses an extension to do its work


```