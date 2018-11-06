How to Use CocoaPods with Swift <!--more-->


⚠️️ DON'T USE COCOAPODS IT'S TOXIC: ⚠️️ [Info here](https://medium.com/xcblog/carthage-or-cocoapods-that-is-the-question-1074edaafbcb)

Heard on swift-lang slack group: `Nah. Carthage all the things. Cocoapods can die in a fire.` 😂

Installing a pod:

0. Instal CocoaPod terminal `sudo gem install cocoapods`
1. Terminal `cd path/to/your/xcodeporject/` hit enter
2. Terminal `pod init` (This creates a Podfile for your project.)
3. Open the podFile Terminal: `open -a Xcode Podfile`
4. In order to use CocoaPods written in Swift, you must explicitly include `use_frameworks!` to opt into using frameworks.
5. Add pod `'Alamofire', '4.4.0'` after `use_frameworks`
6. Terminal: Navigate to your project and:   `pod install`
7. open the project with the .xcworkspace file and not the .xcodeproj, otherwise you’ll encounter build errors.


### Putting your own name on it:

```
target '<Your Target Name>' do
    pod 'Alamofire', '~> 4.7'
end
```


### Terminal tricks
- pwd = show path (print working directory)
- ls -al 👉 Show files in folder
### Semantic versioning
The three numbers are defined as major, minor, and patch version numbers. For example, the version number 0.9.0 would be interpreted as:

| Name | Description |
| --- | --- |
| major | When the major number is increased, this means that non-backwards compatible changes were introduced. When you upgrade a pod to the next major version, you may need to fix build errors, or the pod may behave differently than before. |
| minor | When the minor number is increased, this means new functionality was added, but it’s backwards compatible. When you decide to upgrade, you may or may not need the new functionality, but it shouldn’t cause any build errors or change existing behavior. |
| patch | When the patch number is increased, this means bug fixes were added, but no new functionality was added or behavior changes made. In general, you always want to upgrade patch versions as soon as possible to have the latest, stable version of the pod. |

⚠️️If a version number is less than 1.0.0, it’s considered to be a beta version, and minor number increases may include backwards incompatible changes.⚠️️

[semantic versioning article]()



# Creating a cocoapod lib:

1. `cd desktop`
2. `pod create lib NameOfYourLib`
3. wizard will start, choose swift, demo app and none none,
4. this opens xcode and you have to update to recommended settings in warning section
5. cd NameOFYourLib
6. `git init` 👉 `git add -A` 👉 git commit -m init with cocoa pod
7. Create a repo on github named `NameOFYourLib`
7. git remote add origin https://github.com/eonist/NameOfYourLib.git
8. git push -u origin master (pushes your lib to github)


### Verifying integrity of the pod
1. In your pod dir terminal: `pod lib lint`
2. echo "3.0" >> .swift-version (if there was a swift version bug)
3. Run the lint command again to check for additional errors

### Publication
- Make sure your github releases match the .podspec version number or it will get rejected
- Terminal: `pod trunk register 30n1st@gmail.com ` and also add your Full name in single quotes at the end
- you then get an email from cocoapod to confirm
- terminal pod trunk push NameOfYourLib.podspec

### Testing your pod lib:
- make new xCode project PodTest
- cd to project folder
- pod init -> to create a podfile

### This is a good primer:  
https://www.raywenderlich.com/97014/use-cocoapods-with-swift

CocoaPods has its benefits but I feel Carthage is less intrusive and SPM is the future so I settled on just knowing how CocoaPods work and rather use Carthage and SPM on a daily basis.

### How to Create Cocoa Touch Static Library:

https://www.youtube.com/watch?v=HgzbbS7YjQ8

### Extras
pod --version

### Tips:
Its a good idea to go to cocoapod.com and check if your repo name is taken or not before you decide on a name


Here is my workflow with carthage: [http://eon.codes/2017/01/15/Carthage/](http://eon.codes/2017/01/15/Carthage/)

Said about cocoapods:

https://www.reddit.com/r/iOSProgramming/comments/52l834/cocoapods_vs_carthage/

http://drekka.ghost.io/cocoapods-vs-carthage/
