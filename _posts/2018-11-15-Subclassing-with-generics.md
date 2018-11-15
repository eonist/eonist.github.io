My notes on Subclassing with generics <!--more-->

**Base:**

```swift
class Card<T:CardDataKind>:UIView{
    /**
     * When you set the data diferent UI's are updated
     */
    var data:T?
}
protocol CardDataKind{/*Add required variables here, if needed*/}
```

**Model:**

```swift
struct TopCardData:CardDataKind{
   let title:String
   let content:String
}
struct MiddleCardData:CellDataKind{
   let content:String
   let date:Date
}
struct BottomCardData:CellDataKind{
   let title:String
}
```


**SubClasses:**

```swift
class TopCard:Card<TopCardData> {
   lazy var dateLabel:UILabel = createDateLabel()
   lazy var contentLable:UITextView = createContentLabel()
   /**
    * When you set the data different UI's are updated
    */
   override var data: TopCardData? {
      didSet {
         guard let data:TopCardData = data else {fatalError("data not available")}
         titleLabel.text = data.title
         contentLable.text = data.content
      }
   }
}
```

**Usage**

```swift
let card:TopCard = .init())
card.data = TopCardData(title:"Some title",content:"Some content goes here")
```


**Final note:**

This subclassing with generics also works with UITableView cell dequeuing and registering. See example in the FlowLayout project: [https://github.com/eonist/FlowLayout](https://github.com/eonist/FlowLayout)

<img width="416" alt="img" src="https://rawgit.com/stylekit/img/master/short.gif">
