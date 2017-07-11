some variables can't be created in the init call, the must be created later in the procedure chain.  
Delaying the creation can be done with Lazy Initialization<!--more--> :
 
```swift
lazy var graphics: Graphics = Graphics()//closures also works: {return Graphics()}() 
```
**NOTE:** this can also work with methods and classes with static class methods and closures
