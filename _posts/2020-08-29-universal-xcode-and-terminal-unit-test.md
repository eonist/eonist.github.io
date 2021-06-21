My notes on making unit tests that works in xCode and terminal<!--more-->

## Steps
1. Terminal: swift package init
2. Make XCode MacOS unit-test target in the xcode project
3. Remove references of the unit-test folders xXode created
4. Move the test folder into `Tests/` folder and drag it into xcode (in copy bundle resources remove the info.plist)
5. In the xCode unit-test-target set the info.plist filepath to `Tests/NameOfProjectTests/info.plist`
6. In the package.swift make sure the testTarget name matches the Test target name
