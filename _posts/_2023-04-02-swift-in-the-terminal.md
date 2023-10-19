My notes on swift in terminal<!--more-->

### Advance build:
```bash
xcodebuild test-without-building \
-workspace 'MyProject.xcworkspace' \
-scheme 'QA Automation' \
-destination 'OS=11.0,name=iPhone 6' \
-derivedDataPath /Users/me/Library/Developer/Xcode/DerivedData \
-only-testing:UITesting/testA \
WIREMOCK_PORT='9090'
```

```bash
xcodebuild -verbose test
-workspace MyWorkspace.xcworkspace
-derivedDataPath derivedData
-scheme "MyScheme"
-configuration Debug SYMROOT="$(pwd)"/build
-destination platform="iOS Simulator",name="iPad Air",OS=10.2
-only-testing:UITests/UITests
-resultBundlePath logfiles MY_SETTING="setting_value" MY_OTHER_SETTING="setting_value_2"
```
