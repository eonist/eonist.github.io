<!--more--> 

```swift
class Button {
    var title:String = "The big button"
    var selector: ((sender: AnyObject?, type:String) -> ())?/*this holds any method assigned to it that has its type signature*/
    func click(){
        selector!(sender: self,type: "click")/*call the selector*/
    }
    func hover(){
        selector!(sender: self,type: "hover")/*call the selector*/
    }
}
class View {
    var button = Button()
    init(){
        button.selector = handleSelector/*assign a method that will receive a call from the selector*/
    }
    func handleSelector(sender: AnyObject?,type:String) {
        switch type{
            case "click": Swift.print("View.handleSelector() sender: " + String(sender!.dynamicType) + ", title: " +    String((sender as! Button).title) + ", type: " + type)
            case "hover": Swift.print("View.handleSelector() sender: " + String(sender!.dynamicType) + ", title: " + String((sender as! Button).title) + ", type: " + type)
            default:break;
        }
    }
}
let view:View = View()
view.button.click()/*Simulating button click*/
view.button.hover()/*Simulating button hover*/
//Output: View.handleSelector() sender: Button, title: The big button, type: click
//Output: View.handleSelector() sender: Button, title: The big button, type: hover
```