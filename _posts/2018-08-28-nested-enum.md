My notes on nested enums and how you can use them to navigate UI<!--more-->.

# Basic example:

```swift
enum State {
     enum Normal{
         case search
         case idle
     }
     case normal(Normal)
     case hidden
 }
 /**
  * Set state
	* ## Examples:
	* setState(.normal(.idle))//🤷
	* setState(.normal(.search))//🚀
	* setState(.hidden)//🤦
  */
 func setState(_ state: State){
     switch state {
     case .normal(let normal):
         switch normal {
         case .idle:
              print("🤷")
         case .search:
              print("🚀")
         }
     case .hidden:
         print("🤦")
     }
 }
```

# Another example:

### Description:
- MainView has ContainerView and NavView
- Nav.swift stores the current ViewType state in a static var


### Nav and UI-hierarchy code:

```swift
import Foundation

enum ViewType{
    case receive
    enum Send{
        case clipboard(String)
        case file(String)
        //case current//don't change state, use the one that it is right now
    }
    case send(Send)
}
/**
 * In charge of the state of the app
 */
struct Nav {
    /**
     * Initial state of the app
     */
    private static let defaultViewType:ViewType = {
        let testURL:String = Bundle.main.resourcePath! + "/temp.bundle/" + "test.txt"
        let viewType = Nav.ViewType.send(.file(testURL))
        return viewType
    }()
    static var curViewType: ViewType = Nav.defaultViewType
}
/**
 * Accessor
 */
extension MainView{
    /**
     * Sets the viewState of NavBar and the Container
     */
    func setViewState(viewType: Nav.ViewType){
        navView.setViewState(Nav.curViewType)
        containerView.setViewState(Nav.curViewType)
    }
}
/**
 * ContainerView
 */
extension ContainerView{
    /**
     * Sets the subview of the container
     */
    public func setViewState(viewType:Nav.ViewType){
        if let curSubView = self.curSubView { curSubView.removeFromSuperview() } /*Remove it if it exists*/
        self.curSubView = {
            switch viewType {
            case .receive:
                return createReceiveView()
            case .send/*(let sendType)*/:/*Main*/
                return createSendView(/*viewType:viewType*/)
            }
        }()
        self.addSubview(curSubView!)
    }
}
/**
 * NavigationView
 */
extension NavView{
    /**
     * Sets active tab
     */
    public func setViewState(viewType:Nav.ViewType){
        switch viewType {
        case .receive:
            receiveBtn.setActive(true)
            sendBtn.setActive(false)
        case .send:
            receiveBtn.setActive(false)
            sendBtn.setActive(true)
        }
    }
}
```

### Event code:

```swift
/**
 * Operational
 */
extension NavView{
    /**
     * New
     */
    @objc func onButtonPress(target:AnyObject?){
        if target === receiveBtn {
            Nav.curViewType = .receive
        }else if target === sendBtn {
            Nav.curViewType = .send(.clipboard("the current clipboard blablabla"))
        }
        parent.setViewState(Nav.curViewType)/*Calls the MainView and it sets children's state*/
    }
}

```
