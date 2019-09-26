My notes on how to setup CI travis with a iOS project<!--more-->

### Adding a project to CI-Travis:  
- Go to [https://travis-ci.com/account/repositories](https://travis-ci.com/account/repositories)
- Click manage repositories on github
- Select single repositories and pick the ones you want to add to CI-Travis

### Trigger a CI-Travis build
After configuring the .travis.yml file, push the changes to the repo’s origin. To trigger a build, navigate to the project and select the “More options” menu and then "Trigger Build". The entire process will take a few minutes to complete and if there are no errors will look like the image below. Your git history will not be altered when triggering a build. THe regular way to trigger a build is to make a PR or commit.

### YML

```yml
os:
  - osx
language: swift
sudo: required
osx_image: xcode10.3
xcode_project: CITravisTestExample.xcodeproj # Path to your xcodeproj folder
xcode_scheme: CITravisTestExample
xcode_destination: platform=iOS Simulator,OS=12.2,name=iPhone 8
before_install:
  - export LANG=en_US.UTF-8
  - brew update
  - brew unlink xctool
  - brew install xctool
  - brew link xctool
notifications:
  email:
    on_success: never
    on_failure: change
```

### UnitTest
Add an UnitTest to your project [http://eon.codes/blog/2018/10/11/unit-test/](http://eon.codes/blog/2018/10/11/unit-test/)

### Xcodebuild
Unit-Testing in terminal:  (This way you can test if it works locally first)
```
xcodebuild -project CommentStyler.xcodeproj -scheme CommentStyler -sdk iphonesimulator ONLY_ACTIVE_ARCH=NO
```
or
```
xcodebuild test -project PaginationServiceExample.xcodeproj -scheme PaginationServiceExample -sdk iphonesimulator ONLY_ACTIVE_ARCH=NO
```


UI-Testing in terminal:
```
xcodebuild -project PaginationTableDemo.xcodeproj -scheme "PlaylistPrototype"  -sdk iphonesimulator  -destination 'platform=iOS Simulator,name=iPhone 8,OS=12.2' test
```
Visually showing the UITests:
```
XCode open Developer Tool -> Simulator, while the tests are running in headless mode via xcodebuild. This will show all the simulator screens which are running in headless mode.
```

### Carthage
```
before_install:
  - export LANG=en_US.UTF-8
  - brew update
  - brew unlink xctool
  - brew install xctool
  - brew link xctool
```

### Gotchas
- You might need to make a scheme that is connected to the UITesting target if you have UnitTests and UITests in your project
