Launching multiple tasks in the background and returning the result as they arrive in an orderly fashion:<!--more-->

## Example:

```swift
var arr:[(i:Int,s:String,result:String)] = [(0,"a",""),(1,"b",""),(2,"c",""),(3,"d","")]
var i:Int = 0
func onComplete(/*_ idx:Int,_ result:String*/){
    i += 1
    Swift.print("onComplete: " + "\(i)")
    if(i == arr.count){
        Swift.print("all concurrent tasks completed")
        Swift.print("arr: " + "\(arr)")//[(0, "a", "0a"), (1, "b", "1b"), (2, "c", "2c"), (3, "d", "3d")]
    }
}
for i in arr.indices {
    bgQueue.async {
        let res:String = arr[i].i.string + arr[i].s
        mainQueue.async {
            arr[i].result = res//assinging of values must happen on mainThread
            onComplete()
        }
    }
}
```

## DispatchGroup and DispatchWorkItem

A must if you also need to cancel your async tasks: [https://www.raywenderlich.com/148515/grand-central-dispatch-tutorial-swift-3-part-2](https://www.raywenderlich.com/148515/grand-central-dispatch-tutorial-swift-3-part-2)


## Threading 1 0n 1:

Awesome guy: http://stackoverflow.com/users/4665907/that-lazy-ios-guy-웃 Made a 15min video about Threading in swift 3 just for me:

<iframe width="854" height="480" src="https://www.youtube.com/embed/YhZahnTiA8U" frameborder="0" allowfullscreen></iframe>

## Async vs sync:

Synchronous means that thread that initiated that operation will wait for the task to finish before continuing.

```swift
DispatchQueue.main.sync {

}
```
Asynchronous means that Completes a task in background and can notify you when complete means it will not wait.

```swift
DispatchQueue.main.async {

}
```
