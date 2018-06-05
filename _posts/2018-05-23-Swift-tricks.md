Some of my favorite swift tircks<!--more-->

### Sometimes switch can be overkill:
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
if let fourthItem = (3 >= arr.count ?  nil : arr[3] ) {
   Swift.print("fourthItem:  \(fourthItem)")
}else if let thirdItem = (2 >= arr.count ? nil :  arr[2]) {
   Swift.print("thirdItem:  \(thirdItem)")
}
//Output: thirdItem: 3
```