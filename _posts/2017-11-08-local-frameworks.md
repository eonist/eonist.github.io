Sometimes you want to make an app that isn't using SPM4<!--more-->. Because SPM4 isn't very intuitive at the moment. It's hard to use and lacks updating features. SPM4 is a great way to distribute or ad CI to an app. But it's not great for active development. It can be done but it requires high mental bandwidth to get right. Here is my workflow when not using SPM4

#### 1. Create an iOS t/mac app project in XCode. AwesomeApp  
<img width="576" alt="img" src="https://rawgit.com/stylekit/img/master/makeproject.gif">
#### 2. In the app project go to `file -> new -> target -> Cocoa framework` Name it AwesomeLib  
<img width="588" alt="img" src="https://rawgit.com/stylekit/img/master/addlib.gif">
#### 3. Add code to AwesomeLib/AwesomeLib.swift like `func testing(){print("Hello world")}`  
<img width="574" alt="img" src="https://rawgit.com/stylekit/img/master/addlibcode.gif">
#### 4. Add `@testable import AwesomeLib` to AppDelegate.swift and add `testing()` inside applicationDidFinishLaunching  
<img width="588" alt="img" src="https://rawgit.com/stylekit/img/master/import.gif">  
CMD + R should now print: `hello world` 🎉   

This framework workflow allows Nested dependencies. You can update code in XCode and all dependencies are auto updated when you hit: `cmd + r` (if the dependency has been altered since last compile, if not it isn't re-generated 👌). The workflow is simple, fast and requires low mental bandwidth to setup for new projects and allows an easy way to add thrid-party dependencies. When your project is more mature and your ready to release it you can add SPM-4 to it with ease. That way you can distribute entire apps or submodules later.

### Pro's
- Good for setting up one of personal projects with out too many deps that doesnt need future update. Like prototypes etc
- Good for quick hacks and initial state projects. Use SPM when you transition to a team or make the app community OSS
- Supports syncing to a remote git repo's as you can have .git files in each framework etc

### Con's
- When the dependency graph becomes complicated it gets harder to to keep everything in check, SPM4 will be a better alternative in this case
- does not allow sharing code easily. as other users have to manually setup each dependency, unless you give people the .xcodeproj file
- only allows you to work on 1 project at the time if the libraries are shared with other projects. SPM4 is better in the long run if the app needs regular updates

### Notes:
**NOTE:** To delete targets from your XCode project you go to the app target pan. And click the minus button. Also next to the run button right click -> manage schemes -> remove the library scheme
**NOTE:** To add external pre-existing libraries you can drag the project folder into your app project. And it should work out of the box.
