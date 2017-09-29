Here is how you use Swift package manager in your XCode app projects<!--more--> 

## The workflow:  

1. Terminal: ``cd ~/dev/MyProject/`` 👈 navigate to your project  
2. Terminal: ``swift package init`` 👈 creates the initial SPM files    
3. Add the code from: **The Package.swift file paragraph**  to your newly created Package.swift file:   
4. Terminal: ``swift build`` 👈 downloads the dependencies from github and builds binaries (aka .framework)    
5. Terminal: ``swift package generate-xcodeproj`` 👈  Creates an XCode project that has .framework files  
6. XCode: Open MyProject.xcodeproj and then: file -> Target -> Cocoa app  -> Name it MyApp
7. XCode: Select MyProject -> General -> Targets -> MyApp -> Embedded binaries -> Add the Utils.framework
8. XCode: Add: ``@testable import Utils`` to ``AppDelegate.swift`` and ``print(StringParser.sansSuffix("blue"))`` inside the ``applicationDidFinishLaunching`` method  
9. `cmd + shift + k` to clean your xcode. This is sometimes a needed step if the import statement doesn't work
10. XCode: Run the MyApp and it will now print ``blu``   

## The Package.swift file:
```swift
/*basically adds Swift-utils as a third Party framework in your project    */
import PackageDescription
let package = Package(
    name: "MyProject",
	dependencies: [
		.Package(url: "https://github.com/eonist/swift-utils.git", Version(0, 0, 0, prereleaseIdentifiers: ["alpha", "3"]))
    ]
)
```

## Why is this awesome?

1. When you develop your app. You use binaries but you have full access to third-party source code. 🔑  
2. When you need to download a new version of third party libs: Terminal: ``swift package update``    
3. Supports nested frameworks 👈 "holy grail of Dependency management"   	 
4. Supports CI, ✅  [this](https://www.linkedin.com/pulse/apple-swift-package-manager-deep-dive-shashikant-jagtap) 
5. If you are developing on your own third-party modules you can integrate this workflow with no extra steps. as the packages are pure .git projects. Just commit back to github repo directly from inside your project. Either by ``git checkout master`` or ``git push origin HEAD:master``     
6. If you add code to third-party dependencies then they are embedded in the binaries automatically.   

## Daily driver:

It takes a bit of time to setup. But as your daily driver this is an extremely efficient way to develop apps. As you can update multiple repos from 1 single xcode project. You really only set this up at the start of the app and never again. personally I code for 1 app project but in that same app project i update and commit to maybe 20 other repos on github in various levels of the dependency hierarchy. It makes fixing small libraries really fast and you can do 360 turns really quick when you need to. You can also pretty effortlessly split repos into ever smaller repos making things modular. Its also faster to build this way, as you don't have to keep rebuilding parts of the code that hasn't changed. Xcode can build complex apps with  100k LOC in a matter of seconds. As appose to many minutes if you keep the code as pure source.


## Todo:  
- [ ] Investigate ``buildMetadataIdentifier`` 👈 Supposedly it enables you to target single commit ids rather than version tags
- [ ] Implement CI Travis 
- [ ] C - code in SPM: [here](http://www.bensnider.com/wrapping-c-code-within-a-single-swift-package.html) 