Example of hashing with swift: (The idea is to store Styles in a hash and see if performance goes up) <!--more--> 

```swift
import Foundation

func groupby<T, H: Hashable>(items: [T], f: (T) -> H) -> [H: [T]] {
    return items.reduce([:], combine: { (var ac: [H: [T]], o: T) -> [H: [T]] in
        let h = f(o)
        if var c = ac[h] {
            c.append(o)
            ac.updateValue(c, forKey: h)
        } else {
            ac.updateValue([o], forKey: h)
        }
        return ac
    })
}

let result = groupby(["Charles", "Chen", "Ben", "Boyd", "Brin", "Xavier"], f: { $0.characters.first! })
print(result.dynamicType)//Dictionary<Character, Array<String>>


/**
 * Checks if name exists in the list
 */
func hasName(dictionary:Dictionary<Character, Array<String>>,_ name:String)->Bool{
    let match:[String]? = dictionary[name.characters.first!]?.filter{$0 == name}
    return match?.count > 0
}

hasName(result,"Chen")//true
hasName(result,"Jen")//false

//instead of filter you can also use: forEach

let nums = [-2,-5]
var maxNum:Int?

nums.forEach{maxNum = maxNum != nil ? max(maxNum!,$0) : $0}

print(maxNum)//-2
```

**NOTE:** Look for inspiration in : Introduction to Algorithms 3ed .pdf   
**NOTE:** check treestructure styleresolver.txt as well  (could be more performant than a hash table or a flat structure db)