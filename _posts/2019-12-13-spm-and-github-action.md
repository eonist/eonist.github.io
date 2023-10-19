My notes on using swift package manager and Github actions together<!--more--> ⚠️️ CAUTION ⚠️️ Syntax is extremely sensitive to indentation, don't use tabs etc. Keep the structure github provides

## Adding SPM unit-test to github actions:
> You can also copy pre-existing `.github/` folders and reuse the Test.yml file, just make sure you run `swift test` in terminal before you upload etc. Also add a badge to your readme in the github actions pan on github

1. Terminal: in project-path: `Swift package init`
2. Make Xcode project
3. Add unit-test target in xCode name it: `SomeTest`
4. Drag `/Tests` folder into xCode top level of file-sidemenu (you created Tests/ via SPM)
5. Move the `SomeTest` into Tests folder (you created this in Xcode)
6. In the `test-target` build setting search for info.plist and edit the path to be `Tests/SomeTest/Info.plist`
7. Make sure `swift build` and `swift test` in terminal works (add deps test-target in package.swift)
7. Push to github
8. In `github/repo`: actions -> swift action (change the name to Tests, so the badge makes sense later)
9. Go to actions/tests and click the 3 dotted menu, then click create status badge, add this to your readme
10. Now every time you push, the project is built and tested 🎉

## Add a build badge:
**Code:**  
```markdown
![Swift](https://github.com/eonist/Spatial/workflows/Swift/badge.svg)
```
**Result:**   
![Swift](https://github.com/eonist/Spatial/workflows/Swift/badge.svg)

## Gotchas:
- TestTarget must have unique name if you use them as dependencies of other repos. Or else swift won't know which is correct, So name the TestTarget YourProjectName_OSName_Tests (drop the underscores)
- "Swift test" in terminal builds for macOS
- Having two `.yml` files in a project can be a problem. As can running parallel tests or projects commandline be. Im not sure github spins up new server for each .yml file etc. If it does it might not be a problem.  

## Example:
```swift
name: Swift

on:
  push:
     branches:
     - master
  schedule:
  - cron: "0 12 * * 0-6" #

jobs:
  build:

    runs-on: macOS-latest

    steps:
    - uses: actions/checkout@v1
    - name: Build
      run: swift build -v
    - name: Run tests
      run: swift test -v
```


```yml
on:
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '*/15 * * * *'

┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of the month (1 - 31)
│ │ │ ┌───────────── month (1 - 12 or JAN-DEC)
│ │ │ │ ┌───────────── day of the week (0 - 6 or SUN-SAT)
│ │ │ │ │                                   
│ │ │ │ │
│ │ │ │ │
* * * * *
*	Any value	* * * * * runs every minute of every day.
,	Value list separator	2,10 4,5 * * * runs at minute 2 and 10 of the 4th and 5th hour of every day.
-	Range of values	0 4-6 * * * runs at minute 0 of the 4th, 5th, and 6th hour.
/	Step values	20/15 * * * * runs every 15 minutes starting from minute 20 through 59 (minutes 20, 35, and 50).

on:
  push: #   [push, pull_request]:
     branches:
     - master
  schedule:
  - cron: "0 17 * * 0-6" # 17:00 every day of the week
```

### To use github actions with xcode projects (ios):

```yml
name: CI

on:
  push:
    branches:
      - master
      - develop

jobs:
  build:

    runs-on: macOS-latest

    steps:
    - uses: actions/checkout@master
    - name: Start xcodebuild test
      run: xcodebuild clean test -project MyAppIOS.xcodeproj -scheme MyAppIOS -destination "platform=iOS Simulator,name=iPhone 11 Pro"
```

### To use github actions with xcode projects (macOS):
```yml
name: CI

on:
  push:
    branches:
      - main
   schedule:
      - cron: "0 12 * * 4-4" # Thursdays at 12

jobs:
  build:

    runs-on: macOS-latest

    steps:
    - uses: actions/checkout@main
    - name: Start xcodebuild test
      run: xcodebuild clean build -project MyAppMacOS.xcodeproj -scheme MyAppMacOS
```


### Gotcha:

You can also build from commandline to test before testing in CI:
`xcodebuild clean build -workspace MyApp.xcworkspace -scheme MyAppMac`


### IOS:

building from workspace
```
xcodebuild clean build -workspace MyApp.xcworkspace -scheme MyAppIOS -destination "platform=iOS Simulator,name=iPhone 12 Pro"
```
testing an xcode project
```
xcodebuild clean test -project MyAppIOS.xcodeproj -scheme MyAppIOS -destination "platform=iOS Simulator,name=iPhone 13 Pro Max"
```
ui test:
```
xcodebuild test -project path/to/<project name>.xcodeproj/ -scheme <scheme>
```

### Tests:
Running tests with xcodebuild
xcodebuild is the primary command for both building Xcode projects & running tests and accepts a variety of parameters. The essential xcodebuild command to run a test looks like this:
```
$ xcodebuild
 test
 -project <Your-Project>.xcodeproj
 -scheme <Your-Scheme>
 -destination 'platform=iOS Simulator,name=iPhone 13'
```
- test: Run the test action for the specified scheme. This is like selecting Product > Test in Xcode.
- project or -workspace: The path to your Xcode Project (.xcodeproj) or Xcode Workspace file (.xcworkspace).
- scheme: A scheme is a specific configuration for your target. It defines what happens when you press “Run”, “Build”, “Test”, etc. in Xcode. Each target has at least one scheme by default and you can customize this in Xcode.
- destination: A description of the simulator or physical device you want to run on.

### The Scheme Argument
Xcode lets you customize what should happen when you perform an action, such as “Test”, against a target. Using xcodebuild we can list the available schemes for a given Xcode project or workspace target by running the command.
```
xcodebuild -list -project <Your-Project>.xcodeproj
# OR
xcodebuild -list -workspace <Your-Workspace>.xcworkspace
```

### The Destination Argument
The destination argument accepts a key-value pair string. The first key is the platform, which indicates the OS and if your target is a physical device or simulator. The syntax is key=value,key=value,... and note that there is no space separator between commas.

For the platform key, you can specify any of Apple’s supported platforms (macOS, iOS, watchOS, tvOS), but I’ll focus on iOS. For iOS, we can specify if the platform is a local Simulator or a physical device.

### iOS Simulator
To target the iOS simulator, we can provide a destination string that at minimum should include a platform key set to iOS Simulator and a name key for the Simulator’s name. For example:
```
-destination 'platform=iOS Simulator,name=iPhone 13'
```
You can optionally pass an OS key as well, which is helpful if you have multiple Simulators of the same device type that differ on OS version. For example:
```
-destination 'platform=iOS Simulator,name=iPhone 13,OS=15.2'
```

### iOS Physical Device
To target a physical iOS device, the platform key will be set to iOS and we can provide either a name key to target the device by its name or id to target it by its UDID. Note: either a name or id must be provided, but not both. If you have multiple devices plugged into your machine, id is convenient to use.
```
$ xcodebuild \
 test \
 -project NewTaukTestProject.xcodeproj
 -scheme NewTaukTestProject
 -destination 'platform=iOS,name=Nathan's iPhone'
```
or
```
$ xcodebuild \
 test \
 -project NewTaukTestProject.xcodeproj
 -scheme NewTaukTestProject
 -destination 'platform=iOS,id=00000000-000000000000000'
```

### Clean Project Before Running Tests
If you want to ensure that the Xcode project or workspace is cleaned before running your tests you add the clean command before test. For example:
```
$ xcodebuild \
 clean \
 test \
 -project <Your-Project>.xcodeproj \
 -scheme <Your-Scheme> \
 -destination <Your-Destination>
```
### Running an Individual Test
To run an individual test case from your XCTest suite, you can use the -only-testing argument. The format for this argument is slightly different than the others we’ve shown so far and looks like this:
```
-only-testing:TestBundle/TestSuite/TestCase
```

### Add xcode cli
```
xcode-select --install
xcode-select --print-path
```

### Changing build path and using submodules:
```yml
jobs:
  build:
    runs-on: macos-latest
    steps:
    - uses: actions/checkout@v2
      with:
        submodules: 'true'
    - name: Build
      working-directory: Packages/MyPackage # The working directory path
      run: swift build -v
```

### Resources:
- https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions
- Run from terminal https://mokacoding.com/blog/running-tests-from-the-terminal/

### Todo:
Figure out what the  `-sdk iphonesimulator` does?
