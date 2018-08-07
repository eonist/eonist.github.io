Some of my favorite swift tricks<!--more-->

### Sometimes using switch can be overkill:
```swift
enum State{
    case a,b,c
}
let state:State = .b

if [.a, .b].contains(state) {
    Swift.print("either a or b")
}else if case .a = state {
    Swift.print("must be c")
}
```

### Simple Caching:
```swift
/**
 * Also works for external server data 
 * Example: if let data = myBigData { print(data) }
 */
private var _myBigData : Data? = nil
var myBigData : Data? {
    set (newdata) {
        self._myBigData = newdata
    } get {
        if _myBigData == nil {
            // ... get a reference to file on disk, f ...
            if let d = try? Data(contentsOf:f) {
                self._myBigData = d
                // ... erase the file ...
            }
        }
        return self._myBigData
    }
}
```

### Asserting if an array index exist: 

```swift
let arr = [1,2,3]
if let fourthItem = (3 < arr.count ?  arr[3] : nil ) {
     Swift.print("fourthItem:  \(fourthItem)")
}else if let thirdItem = (2 < arr.count ?  arr[2] : nil) {
     Swift.print("thirdItem:  \(thirdItem)")
}
//Output: thirdItem: 3
```

### Simplify similar code with Closure 

```swift
let closure = { (text:String, bgColor:UIColor, y:CGFloat, action:String) in
   let btn:UIButton = UIButton(type: .system)
   btn.backgroundColor = bgColor
   btn.setTitle(text, for: .normal)
   btn.titleLabel?.font =  .systemFont(ofSize: 12)
   btn.frame = CGRect(x:00, y:y, width:100, height:50)
   btn.addTarget(self, action: Selector(action), for: .touchUpInside)
   self.addSubview(btn)
}
/*btn1*/
closure(
   "Forward",
   .gray,
   250,
   "onForwardButtonClick"
)
/*btn2*/
closure(
   "Back",
   .lightGray,
   250,
   "onbackButtonClick"
)
```

### Action as argument

In the case bellow we use an argument to assign the target. This can only be done with a string

```swift
func createBtn(action:String) -> UIButton {
    let btn:UIButton = UIButton(type: .system)
    btn.addTarget(self, action: Selector(action), for: .touchUpInside)
    return btn
}
@objc func buttonTouched(_ sender:UIButton) {//👈The _ char is imp
    Swift.print("buttonTouched")
}
let btn = createButton(action:"buttonTouched:")//👈The : char is imp
```

### Result (for async callback returns)

improve this: 

```swift
enum Result<Value> {
    case success(Value)
    case failure(Swift.Error)
}

func start(_ completionHandler: @escaping (Result<Any>) -> Void) -> FBSDKGraphRequestConnection{
    return start() { (_, response, error) in
        switch (response, error) {
        case (.some(let result), .none):
            completionHandler(Result(value: result))

        case (.none, .some(let error)):
            completionHandler(Result(error: error))

        case (.none, .none), (.some, .some):
            preconditionFailure("Unexpected State")
        }
    }
}

switch result {
case .success (let value as [ String : Any ]) where value["email"] is String:
    let email = value["email"] as! String
    print("email: \(email)")
default:
    self.present(error: Error(.facebookOther))
} 
```
https://stackoverflow.com/questions/51235876/swift-pattern-matching-switch-downcasting-and-optional-binding-in-a-single-s



### Override static variable

```swift
class var id : String {return "\(HorCell.self)"}/*In a class*/
override class var id : String {return "\(PrimaryCell.self)"}/*In a sub-class of the class*/
```


### String enum's
No need to hard code the string, as long as the enum type is string, the name is auto converted to string when you call rawValue
```swift
enum CellType:String{
   case primary,secondary,tierary
}
print("\(CellType.primary.rawValue)")//primary
print("\(CellType.tierary.rawValue)")//tierary
```


### Accessing raw and hash of enum 

```swift
enum CellType:String{
    case primary,secondary,tierary
}
let possibleCellType = CellType(rawValue: "tierary")
possibleCellType//tierary
possibleCellType?.hashValue//2
```


### Closure Generics 

```swift
typealias UIViewConstraintKind = UIView & ConstraintKind
typealias ReturnType = (anchor:AnchorConstraint,size:SizeConstraint)
typealias ConstraintKindClosure = (_ view:UIViewConstraintKind) -> ReturnType
/**
 * NOTE: We use the "combination-type": `UIViewConstraintKind` since closures can't do regular generics like t:UIView where Self:ConstraintKind
 */
func activateConstraintKind(closure:ConstraintKindClosure) {
   let constraints:ReturnType = closure(self)/*the constraints is returned from the closure*/
   //...do something with the constraints
}
```

### Nifty array trick:

```swift
let result:[String] = Array(repeating: "🎉", count: 3)
print(result)//🎉🎉🎉
```


### Code injection via Protocol extension

```swift
protocol CustomString{
    func doSomething()
}
extension CustomString{
    func doSomething(){
        print("wuu 💥")
    }
}
class A{}
extension A:CustomString{}//👈 you sort of attach CustomString functionality
let a = A()
a.doSomething()//wuu 💥

```