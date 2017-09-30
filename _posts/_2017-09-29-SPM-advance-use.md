My notes Swift package manager 4 <!--more-->

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
1. Edit the dependency url to point to the commit where the fix is: `.revision("74663ec")`
2. `Swift package update` **the latest commit ** has now been included in your project 👌

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
- `.branch("develop")` Branch
- `.revision("74663ec")`  commit hash "revision" (you can use short hash or long hash)




### Use-full SPM commands:

`swift package init --type library` This will create the directory structure needed for a library package
`swift package tools-version` Shows SPM version (there is also: `swift build --version`)


### Use full links:

Overview of SPM by apple:
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