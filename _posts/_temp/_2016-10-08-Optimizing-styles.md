<!--more--> Using a tree style list to store styles did'nt improve speed so it was dropped. Here is the code that was used: 

```swift
//This code was in: StyleManager.swift
static var styleTree:[String:Any] = [:]
 /**
 * Adds @param styles to @param styleTree
 */
static func addStyle(inout styleTree:[String:Any],_ styles:[IStyle]) -> [String:Any]{
    for  style : IStyle in styles{ addStyle(style,&styleTree)}
    return styleTree
}

/**
 * Adds @param style as a branch to @param parentBranch
 */
static func addStyle(style:IStyle, inout _ parentBranch:[String:Any], _ cursor:Int = 0){
    //print("branch.cursor: " + "\(cursor)")
    if(cursor < style.selectors.count) {
        let selectorString:String = SelectorParser.selectorString(style.selectors[cursor])/*creates a string representation of the selector*/
        //print("selectorString: " + "\(selectorString)")
        if(parentBranch[selectorString] == nil) {
            //print("no branch with key: " + "\(selectorString)" + " was found, create new branch and append")
            var newBranch:[String:Any] = [:]
            addStyle(style, &newBranch, cursor+1)
            parentBranch[selectorString] = newBranch
        }else{
            //print("the branch with key: "+ "\(selectorString)" +" exist, append ")
            var existingBranch:[String:Any] = parentBranch[selectorString] as! [String:Any]
            addStyle(style, &existingBranch, cursor+1)
            parentBranch[selectorString] = existingBranch
        }
    }else{
        //print("append branch with style")
        parentBranch["style"] = style
    }
}
```