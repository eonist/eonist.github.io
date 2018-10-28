Some of my favorite swift tricks<!--more-->

### 1. Sometimes using switch can be overkill:
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

### 2. Simple Caching:
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

### 3. Asserting if an array index exist:

```swift
let arr = [1,2,3]
if let fourthItem = (3 < arr.count ?  arr[3] : nil ) {
     Swift.print("fourthItem:  \(fourthItem)")
}else if let thirdItem = (2 < arr.count ?  arr[2] : nil) {
     Swift.print("thirdItem:  \(thirdItem)")
}
//Output: thirdItem: 3
```

### 4. Simplify similar code with Closure

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

### 5. Action as argument

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


### 6. Override static variable

```swift
class var id : String {return "\(HorCell.self)"}/*In a class*/
override class var id : String {return "\(PrimaryCell.self)"}/*In a sub-class of the class*/
```


### 7. String enum's
No need to hard code the string, as long as the enum type is string 👌, the name is auto converted to string when you call rawValue
```swift
enum CellType:String{
   case primary,secondary,tierary
}
print("\(CellType.primary.rawValue)")//primary
print("\(CellType.tierary.rawValue)")//tierary
```


### 8. Accessing raw and hash of enum

```swift
enum CellType:String{
    case primary,secondary,tierary
}
let possibleCellType = CellType(rawValue: "tierary")
possibleCellType//tierary
possibleCellType?.hashValue//2
```


### 9. Closure Generics

```swift
typealias UIViewConstraintKind = UIView & ConstraintKind/*This works almost like someValue:T .... where T:ConstraintKind*/
typealias ReturnType = (anchor:AnchorConstraint,size:SizeConstraint)/*This just makes the method that returns this simpler*/
typealias ConstraintKindClosure = (_ view:UIViewConstraintKind) -> ReturnType
/**
 * NOTE: We use the "combination-type": `UIViewConstraintKind` since closures can't do regular generics like t:UIView where Self:ConstraintKind
 */
func activateConstraintKind(closure:ConstraintKindClosure) {
   let constraints:ReturnType = closure(self)/*the constraints is returned from the closure*/
   //...do something with the constraints
}
```

### 10. Nifty array trick:

```swift
let result:[String] = Array(repeating: "🎉", count: 3)
print(result)//🎉🎉🎉
```


### 11. Code injection via Protocol extension

```swift
protocol CustomString{
    func doSomething()
}
extension CustomString{
    func doSomething(){
        print("wuu 💥")
    }
}
class A{
   func doSomething(){
      print("ahh 👌")
   }
}
extension A:CustomString{}//👈 you sort of attach CustomString functionality
let a = A()
a.doSomething()//wuu 💥

```


### 12. Result (for async callback returns)

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


### 13 Manipulate an object in a closure

```swift
@discardableResult/*👈 Avoids xcode compiler warnings if result is not used*/
func with<T>(_ item: T, update: (inout T) throws -> Void) rethrows -> T {
    var item = item
    try update(&item)
    return item
}

//EXAMPLE 1:
let rectangle = with(CGRect.init(x: 0, y: 0, width: 100, height: 100)) {
  $0 = $0.offsetBy(dx: 20, dy: 20)
  $0 = $0.insetBy(dx: 10, dy: 10)
}
Swift.print(rectangle)// X:30.0, y:30.0, width:80.0, height:80.0

//EXAMPLE 2:
let color = with(UIColor.init(red: 50, green: 100, blue: 0, alpha: 0.9)) { ( col:inout UIColor) -> Void  in
  col = col.withAlphaComponent(0.2)
}
Swift.print(color.cgColor.alpha)//0.2

```


### 14 Use custom closures with the native sortedBy method:

```swift
enum CardType {
  case heart,spades,dimond,clover
}
enum CardValue{
  case two,three,four,five,six,seven,eight,nine,ten,prince,queen,king,ace
}
typealias Card = (type:CardType,value:CardValue)
/**
* arrayTest
*/
func arrayTest(){
  let someCards:[Card] = [(.spades,.eight),(.heart,.seven),(.heart,.king),(.dimond,.ace),(.heart,.two)]
  let findHearts:[Card] = someCards.filter{return $0.type == .heart}
  Swift.print("unsorted.🔵")
  findHearts.forEach{ (card:Card) in
      Swift.print("card.type:  \(card.type) card.value:  \(card.value)")
  }
  Swift.print("unsorted.🔴")
  let sorter:((_ element1:Card, _ element2:Card) -> Bool) = { (element1, element2) -> Bool in
      return element1.value.hashValue < element2.value.hashValue
  }
  let sortedHearts = findHearts.sorted {
      return sorter($0, $1)
  }
  Swift.print("sorted.🔵")
  sortedHearts.forEach{Swift.print("$0.type:  \($0.type) $0.value:  \($0.value)")}
  Swift.print("sorted.🔴")
}
```


### 15. Rethrows:

The rethrows keyword indicates to the compiler that the outer function is a throwing function only if the closure passed in throws an error that is propagated to the current scope. Basically with rethrows, we can use throw inside the closure. When the error handlers are called within the function we use throws.

```swift
typealias MagicalOperation = () throws -> MagicalResult
func doSomethingMagical(magicalOperation:MagicalOperation) rethrows -> MagicalResult {
  return try magicalOperation()
}

//TODO complete this example code with a test
```


### 16. Enums in a closure to describe events


```swift
class MyViewController {

    enum Error: Swift.Error {
        case invalidUsername
        case invalidPassword
    }

    enum Event {
        /// - requestLogin: We are requiring the app to login
        case requestLogin
        /// - showErrorMessage: We errored, we need to show an error message
        case showErrorMessage(Error)
    }

    typealias EventAction = (Event) -> Void

    var eventHandler: EventAction?

}
```

### 17. Delay something
```swift
DispatchQueue.main.asyncAfter(deadline: .now() + 4) {
   /*Do something after 4 seconds have passed*/
}

//Alternatively:

DispatchQueue.global(qos: .background).async {
    sleep(4)
    print("Active after 4 sec, and doesn't block main")
    DispatchQueue.main.async{
        //do stuff in the main thread here
    }
}
```

### Combinational types instead of generics

The `applyConstraint` method requires conformance to UIView and ConstraintKind

```swift
/*Generics*/
func setCardConstraints<T:UIView>(card:T) where T:ConstraintKind{//👈 Looks messy
   card.applyConstraint{ view in
      /*do stuff*/
   }
}
/*Combinational type*/
public typealias UIViewConstraintKind = UIView & ConstraintKind
func setCardConstraints(card:UIViewConstraintKind){//👈 Looks much cleaner
   card.applyConstraint{ view in
      /*do stuff*/
   }
}
```
