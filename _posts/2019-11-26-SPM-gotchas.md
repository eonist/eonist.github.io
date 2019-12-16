My notes on SPM<!--more-->

## Basic gotchas:
`swift package init` creates the init spm project
`swift package init --type library` ⚠️️ untested ⚠️️
`swift build` Builds deps
`swift package generate-xcodeproj` makes xcode proj
`swift package update` updates deps

### Other gotchas:
- Use a framework target in your app project as an umbrella framework so that you don't have to rebuild src code on every build. (Because SPM only handles src code)
- make sure packages build, before you add them as deps. Or else xcode may crash etc

## Target branch:
```swift
dependencies: [
	.package(url: "https://github.com/eonist/B.git", .branch("master"))
]
```

## Define os version
Sometimes some API is only available above some OS version. Specify this in the platforms variable
```swift
import PackageDescription

let package = Package(
    name: "TodoSwiftUIComponents",
    platforms: [.iOS(.v12), .macOS(.v10_13)],
    ...
)
```

## Resources:
[SPM and Commandline targets](https://www.swiftbysundell.com/tips/swift-packages-containing-both-a-command-line-tool-and-a-library/)
[Sundell on SPM](https://www.swiftbysundell.com/articles/managing-dependencies-using-the-swift-package-manager/)
https://developer.apple.com/documentation/xcode/creating_a_swift_package_with_xcode
