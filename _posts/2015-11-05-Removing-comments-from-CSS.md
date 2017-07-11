This is actually really easy, just use the RegExp replace method in swift with this pattern<!--more--> :
```swift
let pattern:String = "\\/\\*.*?\\*\\/"
```
then this: 
```swift
"P{color:#00FF00;}"/*this is a comment*/
```
becomes this:
```swift
"P{color:#00FF00;}"
```