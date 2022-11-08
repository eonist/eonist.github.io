Notes on swift lint<!--more-->

# Swift-lint

1. install swift-lint via swiftlint.pkg (give access in prefs/security): [https://github.com/realm/SwiftLint/releases](https://github.com/realm/SwiftLint/releases)   

2. Add `.swiftlint.yml` to your xcode project folder
  - Example of swift-lint rule set: [https://github.com/realm/SwiftLint/blob/master/.swiftlint.yml](https://github.com/realm/SwiftLint/blob/master/.swiftlint.yml)
  - Another swift-lint example from Sindre Sorhus project GifSki: [https://github.com/sindresorhus/gifski-app/blob/master/.swiftlint.yml](https://github.com/sindresorhus/gifski-app/blob/master/.swiftlint.yml)

3. Add swift-lint script to your xcodeproject:
  - XCode 👉 Build phases
  - Click "plus-button" 👉 Add run script
  - Paste in the bellow:

```swift
if which swiftlint >/dev/null; then
  swiftlint
else
  echo "warning: SwiftLint not installed, download from https://github.com/realm/SwiftLint"
fi
```

4. Build the project to see some swift-lint warnings

5. Add a swift-lint badge to your github project:  
[![SwiftLint Eonist](https://img.shields.io/badge/SwiftLint-Eonist-purple.svg)](https://github.com/eonist/Swift-lint)  
`[![Swift-lint Eonist](https://img.shields.io/badge/swift--lint-eonist-purple.svg)](https://github.com/eonist/Swift-lint)`

6. ✨ Don't forget the terminal auto correct feature Terminal: `swiftlint autocorrect` now renamed to: `swiftlint fix` in your project path. And it auto corrects the entire project ✨ ⚠️️Remember to commit before you auto correct the project. `swiftlint lint` first to assert what will be changed. Before autocorrecting it can be smart to commit current state. and go through commits and assert that everything is good. It can also be a good idea to make sure the package or xcodeproj builds and runs all tests etc


### Notes:
you can ignore or disable swift lint inline (copy the arg name like: empty_enum_arguments fro the warning):
```swift
// swiftlint:disable empty_enum_arguments
(100,         30, 3),
(10000,       30, 333),
(1000000,     30, 33333),
(100000000,   60, 1666666),
(10000000000, 90, 111111111),
// swiftlint:enable empty_enum_arguments

// swiftlint:disable empty_enum_arguments
// swiftlint:enable empty_enum_arguments

// swiftlint:disable all
// swiftlint:enable all
```

## Exclude folders from linting:
```
excluded:
- Pods
- UnitTests/generated/GeneratedMocks.swift
```

## Add swift lint to Packages
https://stackoverflow.com/questions/57461737/how-to-integrate-swiftlint-with-an-ios-app-using-swift-package-manager
