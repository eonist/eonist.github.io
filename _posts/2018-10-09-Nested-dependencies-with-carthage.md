My notes on using nested dependencies with Carthage<!--more-->

### Introduction
This examples shows you how to organize your project so one dependency will use the next in the dependency graph. Instructions w/ gif  how to use and make carthage projects can be found here:  [http://eon.codes/blog/2017/01/15/Carthage/](http://eon.codes/blog/2017/01/15/Carthage/)


### Carthage files:
Repo `B` - Cartfile
```
github "eonist/A" "master"
```
Repo `C` - Cartfile
```
github "eonist/B" "master"
```

### Swift files:
Repo `A` - Demo.swift
```swift
public class AUtil{
   public static func testing(){
      Swift.print("A.AUtil.testing()")
   }
}
```

Repo `B` - Demonstration.swift
```swift
import A

public class Demonstration{
   public static func test(){
      Swift.print("B.test()")
      A.AUtil.testing()
   }
}
```

Repo `C` - DemoTest.swift
```swift
import A
import B

public class DemoTest{
   public static func testing(){
      B.Demonstration.test()
      A.AUtil.testing()
   }
}
```

Repo `C` - ViewController.swift
```swift
import UIKit
import C

class ViewController: UIViewController {
   override func viewDidLoad() {
      super.viewDidLoad()
      C.DemoTest.testing()
   }
}
```

### Output:
```swift
//A.testing()
//B.test()
//A.testing()
```

### Gotchas:
- Make sure Cartfile is in the root of your repo.
- Make sure you follow the how to use carthage tutorial [http://eon.codes/blog/2017/01/15/Carthage/](http://eon.codes/blog/2017/01/15/Carthage/)
- Once you start to release your repos for public consumption, start using tags: Tutorial here: [http://eon.codes/blog/2017/08/04/Git-tagging/](http://eon.codes/blog/2017/08/04/Git-tagging/)
- For simplicity The C Xcode project can contain a framework target and an app target.
- This methodology works for private and public Repos
