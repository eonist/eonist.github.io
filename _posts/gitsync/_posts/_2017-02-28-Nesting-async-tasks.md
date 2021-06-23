My notes on nesting async tasks on a background thread<!--more-->

**⚠️️ This is not the proper way to group Tasks this is considered callback hell, Instead use: DispatchGroup ⚠️️**

```swift

//do 3 things async,
//then in each 3 things do 2 things async
//report back when all inner tasks finish 🍏
//report back when all outer tasks finish 🏁

class ASyncTest {
    var bg = {return DispatchQueue.global(qos: DispatchQoS.QoSClass.background)}()/*Convenience*/
	var main = {return DispatchQueue.main}()/*Convenience*/
    let outerArr = [0,1,2]
    let innerArr = ["a","b"]
    /*Indecies*/
    var outerIdx = 0
    init(){
        func onOuterComplete(_ i_idx:Int, _ e_idx:Int){
            Swift.print("🍏 onOuterComplete \(i_idx), \(innerArr[e_idx]) 🍏")
            outerIdx += 1
            if(outerIdx == outerArr.count){
                allOuterCompleted()
            }
        }
        func allOuterCompleted(){
            Swift.print("🏁 allOuterTasksCompleted: 🏁")
        }
        func doInner(_ i:Int, _ onComplete:@escaping (_ a:Int, _ b:Int)->Void){
            var innerIdx = 0//Array(repeating: 0, count: outerArr.count)//basically just creates this [0,0,0]
            /*Completion handlers resides on the main thread*/
            func onInnerComplete(_ i_idx:Int, _ e_idx:Int){
                Swift.print("🍌 onInnerComplete \(i_idx), \(innerArr[e_idx]) 🍌")
                innerIdx += 1/*increment counter*/
                if(innerIdx == innerArr.count){
                    onComplete(i_idx,e_idx)
                }
            }
            for e in innerArr.indices{
                bg.async{/*do 2 things at the same time*/
                    Swift.print("===🚗 inner async started \(e)===")
                    sleep(IntParser.random(1, 6).uint32)/*simulates task that takes between 1 and 6 secs*/
                    main.async{/*we must jump back on main thread, because we want to manipulate a variable that resids on the main thread*/
                        onInnerComplete(i,e)/*we only itereate on the main thread via this method*/
                    }
                }
            }
        }
        /*The goal here is to fire of all sleep tasks in one swoop on a bg thread, 6 sleep tasks at once. not one after the other*/
        for i in outerArr.indices{
            bg.async {/*do 3 things at the same time*/
                Swift.print("🚄 ---outer async started \(i)---")
                doInner(i,onOuterComplete)
            }
        }
    }
}
/*
Outputs:

🚄 ---outer async started 1---
🚄 ---outer async started 0---
🚄 ---outer async started 2---
===🚗 inner async started 0===
===🚗 inner async started 0===
===🚗 inner async started 1===
===🚗 inner async started 1===
===🚗 inner async started 1===
===🚗 inner async started 0===

👉Until now everything was launched, after this point tasks complete randomly on a random delay👈

🍌 onInnerComplete 2, a 🍌
🍌 onInnerComplete 0, b 🍌
🍌 onInnerComplete 1, a 🍌
🍌 onInnerComplete 0, a 🍌
🍏 onOuterComplete 0, a 🍏
🍌 onInnerComplete 1, b 🍌
🍏 onOuterComplete 1, b 🍏
🍌 onInnerComplete 2, b 🍌
🍏 onOuterComplete 2, b 🍏
🏁 allOuterTasksCompleted: 🏁

*/
```


### DispatchGroup:
Dispatch groups must enter and leave inside async calls on the bg thread. and must have the same amount of enter and leaves.
I suppose one could pass dispatch group refs as a variable and use it in a nested scenario. But DispatchGroups seems to work best when wanting to do many async tasks on bg thread at the same time and have one onComplete for when all tasks complete.

```swift
class ASyncTest {
    var bg = {return DispatchQueue.global(qos: DispatchQoS.QoSClass.background)}()/*Convenience*/
	var main = {return DispatchQueue.main}()/*Convenience*/
    init(){
            let group = DispatchGroup()

            bg.async {/*do 2 things at the same time*/
                group.enter()
                Swift.print("do default")
                sleep(IntParser.random(3, 6).uint32)/*simulates task that takes between 1 and 6 secs*/
                group.leave()
            }
            if "" != "" {
                bg.async{/*do 2 things at the same time*/
                    group.enter()
                    Swift.print("do the first")
                    sleep(IntParser.random(2, 7).uint32)/*simulates task that takes between 1 and 6 secs*/
                    group.leave()
                }
            } else {
                Swift.print("do the second")
            }

            //group.wait()/*wait blocks main thread*/
            group.notify(queue: bg, execute: {
                main.async {/*you have to jump back on main thread to call things on main thread as this scope is still on bg thread*/
                    Swift.print("🏁 group completed: 🏁")
                }
            })
        }
    }
}
```

## Other:
Has custom cancel code: (Operations can be paused, resumed, and cancelled. Once you dispatch a task using Grand Central Dispatch, you no longer have control or insight into the execution of that task. The NSOperation)

https://www.raywenderlich.com/76341/use-nsoperation-nsoperationqueue-swift

Hydra:
https://github.com/malcommac/Hydra

FreshOs/then
https://github.com/freshOS/then
