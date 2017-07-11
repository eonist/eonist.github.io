Drop this in playground and your on your way:<!--more--> 
  
```swift
protocol IPositional:class{}
class A:IPositional{}
class B:IPositional{}
let a:IPositional = A()
let b:IPositional = B()
let c:IPositional = a
a === b //false
a === c //true
```

NOTE: 

the `:class` after the protocol name means that you derive only classes for the protocol, so it cant be a protocol for structs for instance, this in turn enables === operator for the protocol.

It also means that you are able to set variables via extension. 

### Tasks:
- [ ] Write an example of the use case for `variables via extension.`
