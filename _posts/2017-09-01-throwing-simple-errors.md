When do you write throws methods and when do you write methods that return optional. What makes you decide to go for throwing methods? I see some people on github write throwing methods all over the place and others use them less frequently. <!--more--> 

Here is a simple way to throw errors from your methods. You can later refactor to use enums when your code is in a more production ready state.

```swift
extension String: Error {}/*Enables you to throw a string*/

extension String: LocalizedError {/*Adds error.localizedDescription to Error instances*/
    public var errorDescription: String? { return self }
}

func test(color:NSColor) throws{
    if color == .red {
        throw "I don't like red"
    }else if color == .green {
        throw "I'm not into green"
    }else {
        throw "I like all other colors"
    }
}

do {
    try test(color:.green)
} catch let error where error.localizedDescription == "I don't like red"{
    Swift.print ("Error: \(error)")//"I don't like red"
}catch let error {
    Swift.print ("Other cases: Error: \(error.localizedDescription)")/*I like all other colors*/
}
```


