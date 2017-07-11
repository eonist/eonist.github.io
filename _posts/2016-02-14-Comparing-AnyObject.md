array.indexOf in swift only allows Equatables<!--more--> But not all classes are Equatable. It would still be nice to have a way to find their index if stored in an array. Here is a solution that worked for me:

```swift
    class Temp{
        var val:String
        init(_ val:String){
            self.val = val
        }
    }
    
    let a:String = "test"
    let b:AnyObject = a
    let c = b
    let d:AnyObject = 5
    let e:Int = 5
    let f = Temp("abc")
    let g = Temp("123")
    
    Swift.print(c === b)//true
    Swift.print(c === d)//false
    Swift.print(d === (e as AnyObject))//true
    
    /**
     * Returns the index of the first obj that matches the @param item in the @param arr
     * NOTE: this is for comparing references not values
     */
    func indexOf(arr:Array<AnyObject>,_ item:AnyObject)-> Int{
        for var i = 0; i < arr.count; ++i{
            if(arr[i] === item){return i}
        }
        return -1
    }
    
    indexOf([a,b,c,d,e,f,g],d)//3
    indexOf([a,b,c,d,e,f,g],f)//5
    indexOf([a,b,c,d,e,f,g],g)//6
```

**NOTE:** You can add this method to your ArrayExtension class and use it like you would use array.indexOf 

```swift
/**
 * NOTE: only applicable to Array<AnyObject>
 */
extension Array where Element:AnyObject{
    func indexOf(item:AnyObject)-> Int{
        return ArrayParser.indexOf(self,item)
    }
}

//someArray.indexOf(someObj)
```


And for Dictionaries:


```swift
let h:Dictionary<String, Int> = ["Alex": 31, "Paul": 39]
let i:Dictionary<String, Int> = ["Alex": 32, "Paul": 39]

Swift.print(h == i)//false

let temp = [h,i]
    
let index = temp.indexOf {$0 == i} ?? -1//-1 indicates no result found
    
index//1
//
```

## Tasks:
- [ ] indexOf when using dictionaries doesn't work if any of its values is of type AnyObject. Maybe make something that supports this.