Sometimes you want to make an app that isn't using SPM4 <!--more--> Because SPM4 isn't very intuitive at the moment. It's hard to use and lacks updating features. SPM4 is a great way to distribute or ad CI to an app. But its not great for active development. It can be done but it requires high mental bandwidth to get right. Here is my workflow when not using SPM4

1. Create an mac app project in XCode. AwesomeApp
2. In the app project go to `file -> target -> Cocoa framework`
3. Add code to AwesomeLib.swift like `func testing(){print("Hello world")}`
4. add `@testable import Awesome` to AppDelegate.swift and add `testing()` inside applicationDidFinishLaunching
5. CMD + R should now print: `hello world`

This framework workflow allows Nested dependencies. You can update code in xcode and all deps are auto updated when you hit: `cmd + r`. The workflow is simple, fast and requires low mental bandwidth to setup for new projects and allows an easy way to add thridparty deps. When your project is more mature and your ready to release it you can add SPM4 to it with ease. That way you can distribute entire apps or submodules later.


### Pro's
- Good for setting up personal projects with out too many deps.
- Good for quick hacks and initial state projects. Use spm when you transition to a team or make the app community OSS
- supports syncing to a remote git repo

### Con's
- When the dep graph becomes complicated it gets harder to to keep everything in check
- does not allow sharing code easily. as other users have to manually setup each dep


### Notes:
**NOTE:** To delete targets from your xcode project you go to the app target pan. And click the minus button. Also next to the run button right click -> manage schemes -> remove the library scheme
**NOTE:** To add external pre-existing libraries you can drag the project folder into your app project. And it should work out of the box.
