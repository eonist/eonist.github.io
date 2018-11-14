My notes on DispatchGroup <!--more-->

### DispatchGroup:
Dispatch groups must enter and leave inside async calls on the bg thread. and must have the same amount of enter and leaves.
I suppose one could pass dispatch group refs as a variable and use it in a nested scenario. But DispatchGroups seems to work best when wanting to do many async tasks on background thread at the same time and have one onComplete for when all tasks complete.

```swift

import Foundation
@testable import Utils

//do 3 things async,
//then in each 3 things do 2 things async but do something on main-thread when these 4 things are all finished

class ASyncTest {
    var bg = {return DispatchQueue.global(qos: DispatchQoS.QoSClass.background)}()/*Convenience*/
	var main = {return DispatchQueue.main}()/*Convenience*/
    /**
     * Next implement the bellow in your example:
     * TODO: Also research blocks
     */
    init(){
            let group = DispatchGroup()

            group.enter()
            bg.async{/*do 2 things at the same time*/
                Swift.print("do default")
                sleep(IntParser.random(3, 6).uint32)/*simulates task that takes between 1 and 6 secs*/
                group.leave()
            }
            if("" != ""){
                group.enter()
                bg.async{/*do 2 things at the same time*/
                    Swift.print("do the first")
                    sleep(IntParser.random(2, 7).uint32)/*simulates task that takes between 1 and 6 secs*/
                    group.leave()
                }
            }else{
                Swift.print("do the second")
            }

            //group.wait()/*wait blocks main thread, blocks UI, Its important that the notify comes after all enter and leaves has been assigned*/
            /*notify also fires when nothing left or entered*/
            group.notify(queue: main, execute: {/*you have to jump back on main thread to call things on main thread as this scope is still on bg thread*/
                    Swift.print("🏁 group completed: 🏁")//make a method on mainThread and call that instead.
            })
    }
}


```

## DispatchGroup and DispatchWorkItem

A must if you also need to cancel your async tasks: [https://www.raywenderlich.com/148515/grand-central-dispatch-tutorial-swift-3-part-2](https://www.raywenderlich.com/148515/grand-central-dispatch-tutorial-swift-3-part-2)


## Threading 1 0n 1:

Awesome guy:http://stackoverflow.com/users/4665907/that-lazy-ios-guy-웃 Made a 15min video about Threading in swift 3 just for me:

<iframe width="854" height="480" src="https://www.youtube.com/embed/YhZahnTiA8U" frameborder="0" allowfullscreen></iframe>


## Simpler example:

Use DispatchGroups to archive this. You can either get notified when the groups enter() and leave() calls are balanced:

```swift
func myFunction() {
    var a: Int?

    let group = DispatchGroup()
    group.enter()

    DispatchQueue.main.async {''
        a = 1
        group.leave()
    }

    // does not wait. But the code in notify() is run
    // after enter() and leave() calls are balanced

    group.notify(queue: .main) {
        print(a)
    }
}
```

or you can wait (and return):

```swift

func myFunction() -> Int? {
    var a: Int?

    let group = DispatchGroup()
    group.enter()

    // avoid deadlocks by not using .main queue here
    DispatchQueue.global(attributes: .qosDefault).async {
        a = 1
        group.leave()
    }

    // wait ...
    group.wait()

    // ... and return as soon as "a" has a value
    return a
}
```
