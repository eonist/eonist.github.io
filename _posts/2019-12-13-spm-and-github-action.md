My notes on using swift package manager and Github actions together<!--more-->

## Adding SPM unit-test to github actions:

1. Swift package init
2. Make xCode project
3. Add unit-test target
4. Drag /Tests folder into xCode file-pan
5. Move the test-target into Tests folder
6. In the test-target build setting search for info.plist and edit the path to be /Tests/SomeTest
7. make sure `swift build` and `swift test` in terminal works
7. push to github
8. in github/repo: actions -> swift action (change the name to Tests, so the badge makes sense later)
9. Now every time you push, the project is built and tested 🎉

## Add a build badge:
**Code:**  
```markdown
[![Github actions badge](https://github.com/light-stream/Stream-lib/workflows/Builds/badge.svg)](https://github.com/light-stream/Stream-lib/actions)
```
**Result:**   
[![Github actions badge](https://github.com/light-stream/Stream-lib/workflows/Builds/badge.svg)](https://github.com/light-stream/Stream-lib/actions)

## Third party alternative build badge:
**Code:**  
```markdown
[![Github actions badge](https://badgen.net/github/checks/light-stream/Stream-lib?icon=github&label=Build%20Status)](https://github.com/light-stream/Stream-lib/actions)
```
**Result:**  
[![Github actions badge](https://badgen.net/github/checks/light-stream/Stream-lib?icon=github&label=Build%20Status)](https://github.com/light-stream/Stream-lib/actions)
