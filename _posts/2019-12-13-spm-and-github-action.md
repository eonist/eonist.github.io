My notes on using swift package manager and Github actions together<!--more--> ⚠️️ CAUTION ⚠️️ Syntax is extremely sensitive to indentation, don't use tabs etc. Keep the structure github provides

## Adding SPM unit-test to github actions:

1. Terminal: in project-path: `Swift package init`
2. Make xCode project
3. Add unit-test target in XCode name it: `SomeTest`
4. Drag `/Tests` folder into xCode top level of file-sidemenu (you created Tests/ via SPM)
5. Move the `SomeTest` into Tests folder (you created this in xcode)
6. In the `test-target` build setting search for info.plist and edit the path to be `Tests/SomeTest/Info.plist`
7. make sure `swift build` and `swift test` in terminal works (add deps test-target in package.swift)
7. push to github
8. in github/repo: actions -> swift action (change the name to Tests, so the badge makes sense later)
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
- TestTarget must have unique name if you use them as dependencies of other repos. Or else swift wont know which is correct 🤷, So name the TestTarget YourProjectName_OSName_Tests (drop the underscores)
- "Swift test" in terminal builds for macOS

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
      run: xcodebuild clean test -project SentryIOS.xcodeproj -scheme SentryIOS -destination "platform=iOS Simulator,name=iPhone 11 Pro"
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
      run: xcodebuild clean build -project SentryMacOS.xcodeproj -scheme SentryMacOS
```
