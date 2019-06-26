```swift
let dimensionalArray: [Any] = [["a"],[["b","c"],["d","e","f"]],"g","h"]
//print(dimensionalArray[0])
//print(dimensionalArray[1])
//print(((dimensionalArray[1] as! Array<Any>)[1]as! Array<Any>)[1] )


func element(root: [Any], index: [Int]) -> Any? {
   let children = root
   if index.count == 0 { return root }/*returns the root*/
   else if (index.count == 1 && children.count >= index[0]) { return children[index[0]]}/*the index is at its end point, cut of the branch*/
   else if (index.count > 1 && children.count > 0) {
      let newIndex = Array(index[1..<index.count])
      if let childArr: [Any] = children[index[0]] as? Array<Any> {
         return element(root:childArr, index:newIndex)
      } else {
         return nil
      }
   } /*here is where the recursive magic happens*/
   return nil
}

print(element(root: dimensionalArray, index: [1,0,1]))

```

```swift
public typealias MatchCondition = (_ element: XCUIElement) -> Bool
   /**
    * Returns an array of ancestral elements (alt name: heritage)
    * - Parameter condition: a closure that evaluates to true or false
    * - Parameter element: the point to search from
    * ## Example:
    * let imgElement = XCUIApplication().descendants(matching: .image).firstMatch
    * let condition: ElementParser.MatchCondition = { element in element.screenshot().image.size == CGSize(width: 200, height: 50)) }
    * let ancestry: [XCUIElement]? = ElementParser.ancestry(element: imgElement, condition: condition)
    * let ImgElementParent: XCUIElement? = ancestry?.last
    * - Fixme: ⚠️️ Refactor with .map or .flatMap on this method when u have time
    */
   public static func ancestry(root: (index: Int, element: XCUIElement), condition: MatchCondition) -> [(Int, XCUIElement)]? {
      var collector: [(Int,XCUIElement)]? = nil
      let children: [XCUIElement] = root.element.children(matching: .any).allElementsBoundByIndex
      Swift.print("children.count:  \(children.count)")
      for (i, child) in children.enumerated() {
         let metCondition: Bool = condition(child)
         Swift.print("metCondition:  \(metCondition)")
         if metCondition {
            collector = [(i, child)] // found the item, we don't include the actual item we are looking for
            break
         } else if let descendants = ancestry(root: (0, child), condition: condition) { // try to traverse the descendants
            collector = [(i, child)] + descendants
            break
         } // else {
            // collector = nil // dead end, return nil all the way up to the caller
         // }
      }
      return collector
   }
```
