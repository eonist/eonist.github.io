My notes Swift Package Manager 4 <!--more--> Basically this article describes my App Development workflow using SPM 4. And also maintaining the underlying libraries that makes up the app.

⚠️️ I recommend just using google if you are not familiar with some of the terms / names ⚠️️

### What I want:
- To keep code organized and maintainable, it's recommended to break it down into small modules within a module graph or hierarchy. 
- These modules can then be assembled into larger modules as needed, allowing for flexibility and scalability. 
- The root module should be the app itself, with each smaller module building upon it.
- Each module should be easily editable within the project to ensure smooth syncing with GitHub and other version control systems. 
- Third-party modules should be able to update themselves with a simple `swift package update` call, ensuring that the project stays up-to-date with the latest versions of these modules.


### How to get it:
Since SPM 4 doesn't support building to app target yet, we can't call `swift package generate-xcodeproj` more than once, without making a lot of fuzz. That means we have to use SPM as more of a 1 time setup tool and updater of third-party libs.

### Creating a mac app with SPM 4:
1. `cd ~/dev/Awesome/`
2. `swift package init`
3. `swift package generate-xcodeproj`
4. Open the.xcodproj file and `XCode -> file -> Target -> Cocoa app` 👈 **This makes your project a real GUI App project**
5. switch to the AwesomeApp target (next to the run icon)
6. Drag and drop the Awesome.framework into "embedded binaries" for AwesomeApp target
7. add `func testing(){print("Hello world")}` to the Awesome.swift file
8. add `@testable import Awesome` to AppDelegate.swift and add `testing()` inside applicationDidFinishLaunching
9. CMD + R should now print: `hello world`

### Updating dependencies:
You can update dependencies and have the changes show up in Xcode instantly. For instance if you need to update a dependency with a fix, but you don't want to bump the tags. You do this by targeting the commit id or even a development branch in the dependency git project.
1. Edit the dependency url to point to the commit where the fix is located: `.branch("master")`  
2. `Swift package update` **the latest commit in master** has now been included in your project 👌  

### Syncing code back to GitHub:
`swift package edit StringKit` will add a dependency to a Package folder. Which works as an overrider of your "sub-dependency graph". You need to regenerate the Xcode project after you put a dependency into edit mode. Then use git to sync your code to github again.

1: ``cd ~/dev/x/y/z`` 👈 navigate to your module   
2: ``git checkout master`` 👈 switch to the master branch for your module    
3: ``git commit -a -m "Update"``  Make a commit after your change is made  
4: ``git push``   


### The SPM 4 Package.swift file:

```swift
import PackageDescription

let package = Package(
    name: "Awesome",
    platforms: [.iOS(.v12), .macOS(.v10_13)],
    products: [
        .library(
			//This is basically an umbrella
            name: "Awesome",
            targets: ["Awesome"]),
    ],
    dependencies: [
		//this is an external lib that has Some string utils
        .package(url:"https://github.com/futurebright/TestingSPM.git", from: "0.0.1")
    ],
    targets: [
        .target(
            name: "Awesome",
            dependencies: ["TestingSPM"]),//You declare an alias to the dependency url here
    ]
)

```

### Version syntax:
- `from: "1.0.0"` (1.0.0 ..< 2.0.0)
- `from: "1.2.0"` (1.2.0 ..< 2.0.0)
- `from: "1.5.8"` 1.5.8 ..< 2.0.0
- `.upToNextMajor(from: "1.5.8")` 1.5.8 ..< 2.0.0
- `.upToNextMinor(from: "1.5.8")` 1.5.8 ..< 1.6.0
- `"1.2.3"..."1.2.8"`  1.5.8
- `"1.2.3"..<"1.2.6"`  Constraint to an arbitrary open range.
- `"1.2.3"..."1.2.8"` Constraint to an arbitrary closed range.
- `.branch("develop")` Branch (also works with "master" to get the latest code, yessss! 🎉)
- `.revision("74663ec")`  commit hash "revision" (you can also use long hash)


### General tips:
- Don't `swift package generate-xcodeproj` more than 1 time. It will render your Mac App target void
- Use Atom.io to edit the Package.swift file. It has syntax highlighting so you see if you forget double quotes etc
- Avoid hanging commas. Also errors often are an extra ] char so make sure you close all the brackets correctly
- Don't forget wrapping references in `""`

### Use full links:
SPM 4 API: (by apple) 🔑  
https://github.com/apple/swift-package-manager/blob/swift-4.0-branch/Documentation/PackageDescriptionV4.md

Overview of SPM 4 (by apple):    
[https://github.com/apple/swift-package-manager/blob/master/Documentation/Usage.md](https://github.com/apple/swift-package-manager/blob/master/Documentation/Usage.md)

Using local modules (instead of external git url's):
Seems like you have to create a git project locally to get this working. 🤔  
[https://stackoverflow.com/questions/43358706/swift-package-manager-adding-local-dependencies](https://stackoverflow.com/questions/43358706/swift-package-manager-adding-local-dependencies)

SPM 4 with iOS:
[https://github.com/j-channings/swift-package-manager-ios/blob/master/Package.swift](https://github.com/j-channings/swift-package-manager-ios/blob/master/Package.swift)

### Bonus:
**Editable state**   
The intention with Editable state is to be able to edit dependencies in your project. The problem is that this isn't reflected in your xcode project. The only way to reflect this in your xcode project is to call `swift package generate-xcodeproj` Which will then destroy your app target. And you have to set it up all over again. Not a good idea when changes happens 10-100 times per day. This will destroy your productivity.

**Top of Tree Development**
This is better than Editable state. What it does is it creates a local folder of 1 of your deps, and then that overrides all future updates to the entire graph, until you revert it back. You have to still manually drag the folder into Xcode if you don't want to use `swift package generate-xcodeproj` But it allows a workflow where you use third-party libs such as SwiftyJSON and AlamoFire along side your own git managed libs.

**Pinning**   
Pinning is overriding sem ver. Can be relevant for mature and complex graphs to block bad versions etc.

### Questions left unanswered:
- Why is SPM creating all these ...PackageDescription Targets in the xcode-project. They don't do anything and clutters up the project and can be deleted.

- Apple has started to add "forced" sandbox to Xcode projects you create with xcode 9 🙁. To un-sandbox your app, delete the .entitlments file and remove the corresponding setting in build settings. This can be found by going to build settings and searching for signing. Or following the error if you try to build without the .entitlements file. Sandboxing is great. But can cause problems when building / distributing open source software outside "the mac-app-store"
