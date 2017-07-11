Notes on mosby in swift<!--more--> 

**MOSBY explained:**

🖼️.swift (View & controller)
💁.swift (concierge aka presenter)
📊.swift (Model/Database)

**Scenario:**
1. 👀 User clicks and holds down button state: "overDown"
2. 📊weather changes on weather.com
3. 💁Ahh, Rain! 👉 🖼️.button.setState("rainy"+currentState)

**Result:** 
"rainyOverDown" aka a button with water droplets in "over" "down" state

**Problem:**
When the user releases the button. How do we make the state just "rainy" where do we store the rainy state?

**Solution:**
I suppose we can store 2 states internal and external. And combine them to 1 state.

```swift

/*Dual system that uses generics and associatedType to achieve Circular protocol conformance/inference:*/

/*
**IMPORTANT:**   
* In swift Concrete types can have generics
* in swift Concrete & Abstract types can have associatedType 
*/

protocol Viewable {
    func render()
}
protocol Presentable {
    associatedtype ViewType:Viewable// different syntax for Generics in protocols
    var view:ViewType?{get set}
    func setView(_ view:ViewType)
    func informView()
}
extension Presentable{
    func setView(_ view:ViewType) {// we can now have a default setView function
        Swift.print("Presentable.setView() view: \(view)")/*Default method*/
    }
    func informView(){
        Swift.print("Presentable.informView() view: \(view)")/*Default method*/
    }
}
class View<T>:Viewable{
    var presenter:T?
    func render() {
        Swift.print("View.render()")
    }
}
class Presenter:Presentable{
    var view:View<Presenter>?
}
extension Presenter {
    typealias ViewType = View<Presenter>
    func setView(_ view:ViewType){
        Swift.print("Presenter.setView() view: \(view)")
        self.view = view
    }
}
class PresenterX:Presentable{
    var view: View<PresenterX>?
    typealias ViewType = View<PresenterX>
    func setView(_ view:ViewType){
        Swift.print("PresenterX.setView() view: \(view)")
        self.view = view
    }
    func informView() {
        view?.render()
    }
}
class ViewX:View<PresenterX>{
    override func render() {
        Swift.print("ViewX.render()")
    }
}

let view = ViewX()
view.render()//view.render
let presenter = PresenterX()
/*Attaches the view to the presenter*/
presenter.setView(view)//PresenterX.setView() view: ViewX
/*Attaches the presenter to the view*/
view.presenter = presenter
presenter.view?.render()//viewX.render()
view.presenter?.view?.render()//viewX.render()
presenter.informView()//viewX.render()

/*TODO: implement init(view) in presenter instead of setView*/
/*TODO: add view.presenter = self when the view enters init*/



//One can also init with View and Presenter  in the example above
```

It might require subclassing UIView. If you don't want to do that and be just protocol oriented 👌 . Then swap the associatedType and generics. So that presenter uses generics and view uses associatedType. Its sort of a dual system of inference. To not interfere so to speak.
The system feels flexible to use once you test it. You can also override inside extension since UIView extends NSObject.
like so:

### Overriding methods with protocol extensions

This only works with classes that eventually extends NSObject, it will work in future swift versions with out this requirement.


```swift
import Cocoa
  protocol X{
func f() -> A }
  extension X{
      func f() -> A {
          Swift.print("X.f()")
return A() }
  }
  class A:NSView{
      func test(){
          Swift.print("A.test()")
          (self as! B).f()
          y
} }
class B:A{}
  extension A{
      var y:String {
          return "YinA"
      }
      func f() -> A {
          Swift.print("A.f()")
          return A()
} }
  extension B{
      // THESE OVERIDES DO COMPILE:
      override var  y:String {
          Swift.print("B.y")
          return "YinB"
      }
      override func f() -> A {
          Swift.print("B.f()")
          return A()
} }
  let b = B()
  b.test()//A.Test(), B.f(),B.y
```


Could be useful:   

http://chris.eidhof.nl/post/functional-view-controllers/

http://hannesdorfmann.com/android/mosby3-mvi-4