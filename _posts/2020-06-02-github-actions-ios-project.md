My notes on github actions and iOS xcode project<!--more-->

### Steps:
1. Add a test target to your xCode project: `Click "+ button" in targets menu`
2. Add `.yml` file to `.github/workflows/main.yml`
3. Add CI-badge to Readme.md

### YML Example
- It's important to set correct xcodeproj file name, and scheme name
- And set the simulator device you want to use etc

```yml
name: CI

on:
  push:
    branches:
      - master

jobs:
  build:

    runs-on: macOS-latest

    steps:
    - uses: actions/checkout@master
    - name: Start xcodebuild test
      run: xcodebuild clean test -project RichContextMenu.xcodeproj -scheme RichContextMenu -destination "platform=iOS Simulator,name=iPhone 11 Pro"
```

### Add CI-Badge:

`![CI](https://github.com/eonist/RichContextMenu/workflows/CI/badge.svg)`

**Looks like this:**   

![CI](https://github.com/eonist/RichContextMenu/workflows/CI/badge.svg)
