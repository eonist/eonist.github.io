My notes on using swift-lint in XCode<!--more-->.

1. install swift-lint: [https://github.com/realm/SwiftLint/releases](https://github.com/realm/SwiftLint/releases)   

2. Add `.swiftlint.yml` to your xcode project folder
  - Example of swift-lint rule set: [https://github.com/realm/SwiftLint/blob/master/.swiftlint.yml](https://github.com/realm/SwiftLint/blob/master/.swiftlint.yml)
  - Another swift-lint example from Sindre Sorhus project GifSki: [https://github.com/sindresorhus/gifski-app/blob/master/.swiftlint.yml](https://github.com/sindresorhus/gifski-app/blob/master/.swiftlint.yml)

3. Add swift-lint script to your xcodeproject:

  - XCode 👉 Build phases
  - Click "plus-button" 👉 Add run script
  - Paste in the bellow:

```
if which swiftlint >/dev/null; then
  swiftlint
else
  echo "warning: SwiftLint not installed, download from https://github.com/realm/SwiftLint"
fi
```

4. Build the project to see some swift-lint warnings

5. Add a swift-lint badge to your github project [![SwiftLint Sindre](https://img.shields.io/badge/SwiftLint-Sindre-hotpink.svg)](https://github.com/sindresorhus/swiftlint-sindre) `[![Swift-lint Sindre](https://img.shields.io/badge/swift--lint-sindre-brightgreen.svg)](https://github.com/sindresorhus/swiftlint-sindre)`
`

6. Don't forget the terminal auto correct feature `swiftlint autocorrect` in your project path. ⚠️️Remember to commit before you auto correct the project.
