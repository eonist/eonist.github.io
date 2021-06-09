My notes on making responsive layouts with auto-layout<!--more-->

### Priority

- Think of setting priorities to constraints as a way of giving more mass to objects, if they have more priority they manipulate other elements, if they have less, then other lements manipulate their size or position
- You can make elements have dynamic priorities by adding min, max, default priorities to constraints.
- Using priority is a great way to experiment with responsive layouts

### Making responsive autolayout:
// The important part bellow is how w.priority is changed to be less than 500. So that window dont stop resizing etc
```swift
$0.activateConstraints { view in
   let tl = Constraint.anchor(view, to: self, offset: .init(x: 0, y: 48))
   let w = Constraint.width(view, to: self)
   w.priority = NSLayoutConstraint.Priority(rawValue: 250)
   let minW = Constraint.width(view, width: 320, multiplier: 1, relation: .greaterThanOrEqual)
   minW.priority = NSLayoutConstraint.Priority(rawValue: 300)
   let maxW = Constraint.width(view, width: 420, multiplier: 1, relation: .lessThanOrEqual)
   maxW.priority = NSLayoutConstraint.Priority(rawValue: 300) // NSLayoutConstraint.Priority.windowSizeStayPut //  // // NSLayoutConstraint.Priority(rawValue: 1000)
   let h = Constraint.height(view, height: 48)
   return [tl.x, tl.y, h, w, minW, maxW]
}
```

## Store priorities in an enum
It's a good idea to store priorities in an enum.
This way you have an overview where you can easily debug which priorities should be changed.
```swift
/**
* - Note: Priorities organized in enum (easier overview)
*/
enum Priorities {
  /**
	* ## Examples:
	* Priorities.highlight
	*/
  typealias Priority = NSLayoutConstraint.Priority
  static let rowDefault: Priority = .init(rawValue: 490)
  static let rowMax: Priority = .init(rawValue: 600)
  static let highlight: Priority = .init(rawValue: 50)
  static let container: Priority = .init(rawValue: 240)
  static let iconMin: Priority = .init(rawValue: 400)
  static let labelDefault: Priority = .init(rawValue: 235)
  static let labelMin: Priority = .init(rawValue: 500)
  static let tagMin: Priority = .init(rawValue: 400)
}
```
## Gotchas:
- You can add min-stop, max-stop, default value etc to constraints, these are called .greaterThanOrEqual, .lessThanOrEqual and .equal
- Setting priorities above and bellow 250 can change things a lot
- Sometimes you have to play a bit with priorities to get things right

## Resources:

- More complex responsiveness with compression and resistance (relevant for text etc): 2019-04-23-Compression-resistance-priority-autolayout.md
- General Autolayout: 2018-12-17-Programmatic-autolayout.md
