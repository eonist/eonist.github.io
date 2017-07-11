My notes on Protocol Inheritance<!--more--> 

insert class hierarchy diagram here.

## Rational

Say you have 3 classes that needs to call the same method with the same code. But you cant extend a new BaseClass. Because the BaseClass is different in each 3 classes. You need to override the same method in all classes "scroll" And you want to be able to call the "super" There is no super with pop (protocol oriented programming)


```swift
/**
 * This setup describes how classes may call methods and the methods call their "super" method. 
 * Inheritance with protocols. retaining the ability to call "super" and the ability to override
 */
protocol Scrollable{
    func scroll()
}
extension Scrollable{
    func scroll(){
        defaulScroll()
    }
    func defaulScroll(){
        Swift.print("a")
    }
}
class A:Scrollable{}
protocol BScrollable:Scrollable{}
extension BScrollable{
    func scroll() {
        bScroll()
    }
    func bScroll(){
        Swift.print("b")
        defaulScroll()
    }
}
class B:BScrollable{}
//how do we make c call b call a
protocol CScrollable:BScrollable{}
extension CScrollable{
    func scroll() {
        cScroll()
    }
    func cScroll(){
        Swift.print("c")
        bScroll()
    }
}
class C:CScrollable{}

let a = A()
a.scroll()//a
print("")
let b = B()
b.scroll()//b, a
print("")
let c = C()
c.scroll()//c , b , a

```


## An alternative is to : 

```swift
protocol X{}
protocol Y{}
protocol Z{}
extension X{func render(print("x")){}}
extension Y{func render(print("y")){}}
extension Z{func render(print("z")){}}
class A:X,Y,Z{
    func render(){//can be called from a Super class
        (self as X).render()
        (self as Y).render()
        (self as Z).render()
    }
}
let a = A()
a.render()//z,x,y
```