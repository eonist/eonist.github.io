Reducing for-loops is a great way to maintain readability and maintain code modularity. **"Data science"** methods like map, flatMap, filter and reduce are awesome building blocks for **functional programing in swift**, but you can also build your own custom methods that do similar things. <!--more--> Here is a trick were we use closure blocks to encapsulate the method call. The for-loop is the same but the method call inside the loop is what you wan't it to be. This approach is a great way to avoid multiple for-loops that do fairly the same thing. All you do is pass the for-loop the method you want it to perform. The coolest thing about this is that you can use any variable or method from the class scope you create the action in and add it to the action closures. It all magically works! In this example we pass it **2 different methods** "action1" and "action2": 

```swift
import Foundation

class Brick{
    var active:Bool
    var focused:Bool
    var name:String
    init(_ active:Bool,_ focused:Bool, _ name:String){
        self.active = active
        self.focused = focused
        self.name = name
    }
    func isActive()->Bool{
        return active
    }
    func isFocused()->Bool{
        return focused
    }
}
private class Utils{
    static func performAction(bricks:[Brick], _ action:(Brick)->Bool)->Brick?{//TODO: the brick bool part you should make into a typealias
        for brick in bricks{
            if(action(brick)){
            	return brick
            }
        }
        return nil
    }
}

let a:Brick = Brick(true,false,"a")
let b:Brick = Brick(false,true,"b")
let c:Brick = Brick(true,true,"c")
let bricks = [a,b,c]

let action1:(Brick)->Bool = {$0.isActive()}
let action2:(Brick)->Bool = {$0.isFocused()}

print(Utils.performAction(bricks, action1)!.name)//a
print(Utils.performAction(bricks, action2)!.name)//b
```

Drop the above code snippet into XCode playground and test it for your self. 


