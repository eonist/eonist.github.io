My notes on using swift package manager and Github actions together<!--more-->

## Adding SPM unit-test to github actions:

1. Swift package init
2. make xcode project
3. add unit-test target
4. drag /Tests folder into xcode file-pan
5. move the test-target into Tests folder
6. in the test-target build setting search for info.plist and edit the path to be /Tests/SomeTest
7. make sure `swift build` and `swift test` in terminal works
7. push to github
8. in github/repo: actions -> swift action (change the name to Tests)
9. Now everytime you push, the project is built and tested 🎉

## Add a build badge:
**Code:**  
```markdown
[![Github actions badge](https://github.com/light-stream/Stream-lib/workflows/Builds/badge.svg)](https://github.com/light-stream/Stream-lib/actions)
```
**Result:**   
[![Github actions badge](https://github.com/light-stream/Stream-lib/workflows/Builds/badge.svg)](https://github.com/light-stream/Stream-lib/actions)
