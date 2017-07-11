Notes on mosby in swift<!--more--> 



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


Could be useful:   


http://chris.eidhof.nl/post/functional-view-controllers/
