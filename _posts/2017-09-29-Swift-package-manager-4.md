My notes Swift Package Manager 4 <!--more-->  

⚠️️This article is currently a WIP⚠️️

### Creating a mac app with SPM 4:
1. `cd ~/dev/Awesome/` 
2. `swift package init`
3. `swift package generate-xcodeproj`
4. XCode -> file -> Target -> Cocoa app 👈 **This makes your project a real GUI App project**
5. switch to the AwesomeApp target (next to the run icon)
6. Drag and drop the Awesome.framework into "embedded binaries" for AwesomeApp target
7. add `func testing(){print("Hello world")}` to the Awesome.swift file
8. add `import Awesome` to AppDelegate.swift and add `testing()` inside applicationDidFinishLaunching
9. CMD + R should now print: `hello world`

### Updating dependencies: 
You can update dependencies and have the changes show up in Xcode instantly. For instance if you need to update a dependency with a fix, but you don't want to bump the tags. You do this by targeting the commit id or even a development branch in the dependency git project.
1. Edit the dependency url to point to the commit where the fix is located: `.branch("master")`
2. `Swift package update` **the latest commit in master** has now been included in your project 👌

### Syncing code back to GitHub: 
In SPM 4 Apple seems to lock the .git folder of dependencies. So you need to first unlock it. Unlock the .git folder read my tutorial [here](https://stackoverflow.com/a/46505143/5389500) There a way to do this via terminal: `sudo chmod -R 777 .` If you have already edited source I recommend "stashing" the files and reseting to head `git reset --hard`. 

1: ``cd ~/dev/x/y/z`` 👈 navigate to your module 
2: ``git checkout master`` 👈 switch to the master branch for your module  
3: ``git commit -a -m "Updated Feature-x"``  Make a commit after your change is made
4: ``git push``  

You might need to check if git remote is correct: `git remote -v` If you need to set `git remote url` see: https://stackoverflow.com/questions/42830557/git-remote-add-origin-vs-remote-set-url-origin The bad news is that every-time you do `swift package update`. You have to do the unlocking of files and step 1-4 again 🙁 There might be something that can avoid this. I think I have to look into the new pinning and editable functionality in SPM4, as the current resetting of git data is not a very efficient way to work... To be continued tomorrow ✌️ 


### The SPM 4 Package.swift file:

```swift
import PackageDescription

let package = Package(
    name: "Awesome",
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


### Use-full SPM commands:

`swift package init --type library` This will create the directory structure needed for a library package
`swift package tools-version` Shows SPM version (there is also: `swift build --version`)


### General tips:
- Don't `swift package generate-xcodeproj` more than 1 time. It will render your Mac App target void
- Use Atom.io to edit the Package.swift file. It has syntax highlighting so you see if you forger double quotes etc

### Use full links:

Overview of SPM 4 by apple:
https://github.com/apple/swift-package-manager/blob/master/Documentation/PackageDescriptionV4.md

Using local modules (instead of external git url's): 
Seems like you have to create a git project locally to get this working. 🤔
https://stackoverflow.com/questions/43358706/swift-package-manager-adding-local-dependencies

SPM 4 with iOS: 
https://github.com/j-channings/swift-package-manager-ios/blob/master/Package.swift



### Bonus:
**Editable state**   
`Seems a bit complex, research this`
https://github.com/apple/swift-package-manager/blob/master/Documentation/Usage.md#editable-packages

**Pinning**   
`Seems a bit complex,Needs Research`