My notes on Swift package manager + XCode + Nested frameworks<!--more--> 

## Pretext
Lets make an awesome HackerNewsApp in 2 days. For that you need to be modular and re-use other peoples code. We need [AlamoFire](https://github.com/Alamofire/Alamofire)  for http, [SwiftJSON](https://github.com/SwiftyJSON/SwiftyJSON)  for json and [Element](https://github.com/eonist/Element)  for GUI. 

## Nested frameworks
The chain of dependencies looks like this: Its only 3 level deep: (It can go deeper if needed)   

<img width="400" alt="img" src="https://rawgit.com/stylekit/img/master/hacker_news_SPM_diagram.svg">

## Manifest 

Inside your Package.swift file you write this:  

```swift
import PackageDescription
let package = Package(
    name: "HackerNews",
	dependencies: [
		/*Downloads Element + swift-utils*/
		.Package(url: "https://github.com/eonist/Element.git", Version(0, 0, 0, prereleaseIdentifiers: ["alpha", "5"])),
		/*Downloads AlamoFire*/
		.Package(url: "https://github.com/Alamofire/Alamofire.git",MajorVersion:4,minor:3),
		/*Downloads SwiftyJSON*/
		.Package(url: "https://github.com/SwiftyJSON/SwiftyJSON.git",MajorVersion:3,minor:1)
    ]
)
```

Where is the [swift-utils](https://github.com/eonist/swift-utils) dependency you might ask? When you download [Element](https://github.com/eonist/Element)  SPM will also look inside a Package.swift file that is in the Element repo on github. And automatically start downloading the swift-utils dependency.

## Workflow

1. Terminal: ``cd dev/HackerNews``
2. Terminal: ``swift package init`` 👈 Creates boilerplate Package.swift etc
3. Replace the content inside HackerNews/Package.swift with the code written in the [Manifest paragraph](#manifest) 
4. Terminal: ``swift build`` 👈 Downloads and builds all the dependencies. 
5. Terminal: ``swift package generate-xcodeproj`` 👈 Creates .xcodeproj
6. Follow my [Tutorial](http://stylekit.org/blog/2017/02/05/Xcode-and-spm/)  on how to Create an SPM App project from a SPM created .xcodeproj file
7. Once you have your first "Hello world" going. Start adding some innovative UX ideas to your App project. 
8. Need to update dependencies or add new ones? Just edit your Package.swift file and Terminal: ``swift build`` 
9. To add your app project to Github all you do is make a new project on github.com and add a .gitignore file that ignores /.build, /Tests,/ Packages and .xcodeproj. Then you can stay in-sync with other team-members etc. 

## Final word  
I would love to include some HackNewsApp Example code. And I plan to. Until then you have to figure out how to use AlamoFire, Element and SwiftyJSON your self. At least now you have a basic foundation on how to start making Apps with SPM. 