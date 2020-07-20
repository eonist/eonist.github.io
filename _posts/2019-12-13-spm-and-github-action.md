My notes on using swift package manager and Github actions together<!--more-->

## Adding SPM unit-test to github actions:

1. Terminal: in project-path: `Swift package init`
2. Make xCode project
3. Add unit-test target in XCode name it: `SomeTest`
4. Drag `/Tests` folder into xCode top level of file-sidemenu (you created Tests/ via SPM)
5. Move the `SomeTest` into Tests folder (you created this in xcode)
6. In the `test-target` build setting search for info.plist and edit the path to be `/Tests/SomeTest/`
7. make sure `swift build` and `swift test` in terminal works
7. push to github
8. in github/repo: actions -> swift action (change the name to Tests, so the badge makes sense later)
9. Now every time you push, the project is built and tested 🎉

## Add a build badge:
**Code:**  
```markdown
![Swift](https://github.com/passbook/Key/workflows/Swift/badge.svg)
```
**Result:**   
![Swift](https://github.com/passbook/Key/workflows/Swift/badge.svg)

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
