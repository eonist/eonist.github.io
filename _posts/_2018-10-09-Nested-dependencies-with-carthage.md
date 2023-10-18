My notes on using nested dependencies with Carthage<!--more--> ⚠️️ This article is not relevant anymore. Use swift package manager ⚠️️

### Introduction
This article shows you how to organize your project so one dependency will use the next in the dependency graph. Instructions w/ gif  how to use and make carthage projects can be found here:  [http://eon.codes/blog/2017/01/15/Carthage/](http://eon.codes/blog/2017/01/15/Carthage/)

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
public class Demo{
   public static func test(){
      Swift.print("\(self).test()")
   }
}
```

Repo `B` - Demo.swift
```swift
import A

public class Demo {
   public static func test() {
      Swift.print("\(self).test()")
      A.Demo.test()
   }
}
```

Repo `C` - Demo.swift
```swift
import A
import B

public class Demo{
   public static func test(){
      Swift.print("\(self).test()")
      B.Demo.test()
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
      C.Demo.test()
   }
}
```

### Output:
```swift
//C.Demo.test()
//B.Demo.test()
//A.Demo.test()
```

### Birds-eye-view:
- Getting in the habit of making projects with nested dependencies can greatly speed up build times, enable team members to contribute to your project more easily and enable you to share code in modules on github more easily.

- You can update the source files of **A** or **B** and just push them to github and they will be included if you execute `carthage update` in the **C project**. No need to re-build etc in A and B. You can also target single/multiple deps. by calling: `carthage update A B`

<img width="568" alt="img" src="https://rawgit.com/stylekit/img/master/carthagebuilding.gif">

- If you want to work on FrameWork A inside The C project, then add the src files in the `C/Carthage/Checkout`  folder and follow this tutorial on how to build a local framework from src : [http://eon.codes/blog/2017/11/08/local-frameworks/](http://eon.codes/blog/2017/11/08/local-frameworks/)


### Gotchas:
- Make sure `Cartfile` is in the root of your repo.
- Make sure `Cartfile` is with an uppercase C, if you use lowercase the graph won't work 🔑
- Make sure you follow the how to use carthage tutorial [http://eon.codes/blog/2017/01/15/Carthage/](http://eon.codes/blog/2017/01/15/Carthage/)
- Once you start to release your repos for public consumption, start using tags: Tutorial here: [http://eon.codes/blog/2017/08/04/Git-tagging/](http://eon.codes/blog/2017/08/04/Git-tagging/)
- For simplicity The C Xcode project can contain a framework target and an app target.
- This methodology works for private and public Repos
