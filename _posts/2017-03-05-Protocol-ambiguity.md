How to differentiate protocol ambiguity<!--more-->
```swift
protocol X{}
extension X{
    func render(){
        Swift.print("X")
    }
}
protocol Y{}
extension Y{
    func render(){
        Swift.print("Y")
    }
}
class A:X,Y{//👈 Conforms both X and Y
    func renderX() {
        (self as X).render()
    }
    func renderY() {
        (self as Y).render()
    }
}
let a = A()
a.renderX()//x
a.renderY()//y
```