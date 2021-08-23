My notes on using local SPM packages<!--more-->

### PRO:
- Instant updates / faster builds (iterate faster) ✨
- Keep modules in packages (encapsulation of code) ✨
- Less remote downloads and network congestion and waiting times ✨
- Keep a tree of packages and make them relative to each other
- Keep many cross platform projects in the root folder. and reuse code in the hirarchy
- You can still have spm tests in sub deps (and probably github actions if you wire it correctly)
- Updates to asset files in packages
- Repos dont share code with each other explicitly (isolation) packages as well

### CON:
- Must be version controlled in the main proj
- bigger main git repo 👈
- relative paths can be a bit of chore to manage over time
- More internal dependencies means less opportunity to easily add github actions CI tests and flags to deps
- Over time, sub xcode projects needs to have their files readded etc
- XCode renaming of instances sometimes gets to eager

### XCode
1. Make an SPM package
2. Move the package into an xCode proj root folder
3. Drag the package into xCode proj in xCode
4. xCode - target - general - embed libraries and framework. click + and add package
5. You can now import code from package in your main proj

### Gotchas:
- If a package can be used in other projects then keep the repo remote and standalone
- If a package should have CI and github actions etc. Then keep the repo remote and standalone
- If a package just need to be in its own namespace, and is built only for one project. then keep it as a local dependency repo inside main project folder
- if a package requires frequent updates. it might be best to use local dep
- if a change isn't reflected in dependant repos -> right click on package and press update package
- xCode search becomes available across all submodules when using such an umbrella structure 👌

### Umbrella structure:
- MainProject xCode workspace
	- package.swift (spm tests etc)
	- App ios  (src code for ios app)
	- App macOS (src code for mac app)
	- packages/
		- iOS/ (packages for macOS)
		- mac/ (packages for iOS)
		- shared/ (packages for iOS and macOS)

### Package
1. Keep the package in the root of the proj
2. Add to dependencies: `.package(path: "CalculatorCore"),` In the Package.swift
3. Add to target dependencies: `"CalculatorCore"` In the Package.swift

### Resources:
- https://developer.apple.com/documentation/swift_packages/organizing_your_code_with_local_packages
- https://developer.apple.com/documentation/swift_packages/developing_a_swift_package_in_tandem_with_an_app

### Todo:
- Figure out how to use xCode swift package with local relative path. (the one we use for remote packages) (seems like this is not possible)
