The solution to the problem bellow is to name your variables differently than your implicit setter<!--more--> :
```swift
protocol IPositional{
   var position:CGPoint
   setPosition(position:CGPoint)
   getPosition():CGPoint
}
class A{
   var position:CGPoint{get{return frame.origin}set{frame.origin = newValue}}
   func setPosition(position:CGPoint){
      //this wont compile, because swift occupies the setPosition method call if your variable is in the for of: {get{} set{}}, it will work if your variable is a regular variable
   }
}
```

Solution: rename your variable to var pos:CGPoint and the problem goes away.
