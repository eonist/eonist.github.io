Notes on Swift package manager<!--more--> 

## What is it:  
SPM automates the process of downloading, compiling, and linking dependencies. 

## Pretext:  
Developing for apple devices requires you to use either Cocoapod Carthage or SPM. Which can be divided into 3 camps: The past, the present and the future. SPM being the future. 

## Folder structure:

[insert illustration here]

## First impression using SPM:

SPM seems like a command-line tool for downloading projects that contain .swift files from github. It's able to derive files based on "Sem-Ver" tagging of committed code. I think it also supports nested dependencies but I haven't tested that part. And thats pretty much it. Which leaves a lot to be desired. Like support for binaries (.framework)? Which is really important when dealing with third-party libs. (binaries are a lot faster to compile that source code)

## Export: 
1. Add an empty Package.Swift to your xcode project
```swift
import PackageDescription
let package = Package(
  name: "Element"
)
```
2. keep all other .swift files you want to include in the package in a folder named Sources
3. Upload the Sources folder and the Package.swift file to github


## Import: 

1. Add a Package.swift to your xcode project with example: 
```swift
import PackageDescription
let package = Package(
    name: "Element",
    dependencies: [
        .Package(url: "https://github.com/eonist/Element.git", majorVersion: 1)/*<--1.0.0..<2.0.0*/
    ]
)
```
 
Pick versions with this syntax: ``majorVersion: 0, minor: 4`` For more specific version picking use: ``Version(0, 0, 0, prereleaseIdentifiers: ["alpha", "3"])`` which would download ``0.0.0-alpha.3`` for ranges you can use syntax such as : ``Version(2, 0, 1) ..< Version(2, 1, 0)``



2. navigate to the folder in terminal ``cd ~/Documents/dev/SomeProject``  
3. in terminal execute ``swift build``

## Excluding non-source files:  
Use this property to exclude files and directories from the package sources.  
```swift
let package = Package(
    name: "Foo",
    exclude: ["Sources/Fixtures", "Sources/readme.md", "Tests/FooTests/images"]
)
```

## Create .xcodeproj file via terminal:
Each SPM-enabled project can be converted to an Xcode project:  
cd to the respected folder
```bash
swift package generate-xcodeproj
```

## SPM dependency resolving: 

If two packages depend on different versions of a third package, the package manager tries to find a version of that package that both will find acceptable.

## Drawbacks:

1. Unable to target Commit ids. Only release tags are supported. Which makes it difficult to have a fast workflow when evolving your projects. Releases should be significant and not iterate on every new commit. If you have a lot of nested frameworks which you should because modularity is good and thats why we have dependency managers in the first place. Apple has no intention to support targeting commit ids according to their mailing-list on SPM. Carthage has support for targeting commit ids and even ``"HEAD"`` However pushing a release tag isn't that much work but it is inconvenient. 

2. Difficult to update dependencies once xcodeproj is generated

## Side notes:  
- Target beta/alpha versions: ``Version(0, 0, 0, prereleaseIdentifiers: ["alpha", "2"])``
- The 'Sources' dir could also be 'Source', 'src' or 'srcs'
- In terminal: ``swift build --help`` shows help options in terminal
- In terminal: ``swift package`` shows all terminal commands available in SPM
- In terminal: ``swift build --version`` prints ``Apple Swift Package Manager - Swift 3.0.2 (swiftpm-11750)``
- You should be able to target individual commit ids via: buildMetadataIdentifier in Version. Google it 🔑
- in terminal: ``swift package init --type=library`` Creates a SPM starter kit
- In terminal: ``swift package test`` tests your SPM project 
- In terminal: ``swift package init --type executable`` makes an SPM starter kit that can be called from terminal
- In terminal: ``.build/debug/<Package-Name>`` will execute your project if its executable and has been built
- In terminal: ``swift package init --type=library`` creates a testable setup. Run ``swift test`` after
- In terminal: ``swift package fetch``  Swift package has commands to fetch the dependencies 
- In terminal: ``swift package update`` Swift package has commands to update the dependencies 

## Epiphanies:
1. SPM forces you to organize your folder structure the "SPM way", I didn't like this at first. Source/<project-name>/<file-name>.swift but if you only have one 'module' which you should. Modularity and all that. Then github.com will not force you to jump through 2 levels to see your files. as github skips 1 level if a folder is inside another folder. So you just click sources and there is all your files. all in 1 click. NICE! 🔑  
2. If you actively change your dependencies' code then don't link to binaries directly, instead include their source code as sub-project. When its code base becomes stable start using binaries aka (.framework).
3. The target in the SPM manifest is used when you include internal dependencies in the Source file see : [this](http://www.bensnider.com/wrapping-c-code-within-a-single-swift-package.html) 

## Final word:
We are all DevOps now. There is no getting around this, if you want to code efficiently you have to master the art of DevOps. Or descend into "dependency hell". Package Dependency managers Are not easy to use, but one cannot live with out them. 

## Links:

[basic swift info video](https://honzadvorsky.com/articles/2016-06-30-19-00-nslondon_swift_package_manager/) 
[Apples docs on SPM:](https://github.com/apple/swift-package-manager/blob/master/Documentation/Reference.md#source-layouts) 
[SPM + CI](https://www.linkedin.com/pulse/apple-swift-package-manager-deep-dive-shashikant-jagtap) 
[Unit test](https://dzone.com/articles/unit-testing-with-swift-pm) 
[advance spm](https://www.cocoawithlove.com/blog/package-manager-fetch.html) 

## Todos:
- [x] Figure out how SPM can build ``importable frameworks`` into an XCode app project ✅
- [ ] Figure out how you can bring Existing apps into the SPM framework fold 👈
- [ ] test hybrid framework if they rebuild on run or not