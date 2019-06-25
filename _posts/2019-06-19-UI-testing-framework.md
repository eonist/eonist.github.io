My notes on setting up a UI-testing-framework<!--more-->

## 100% Carthage .framework support
- Add the `UITestSugar.framework` to the Test-project folder 👉 drag it to the frameworks folder. 👉 select the UITestTarget
- In The UITestTarget  👉 build-phases 👉 click plus-button 👉 Copy files 👉 drag the .framework file into that panel 👉 make sure destination is set to framework.
- In the UITestSugar project: Add `$(PLATFORM_DIR)/Developer/Library/Frameworks` to the in "Build settings" 👉 "framework search paths"
- Then add the magic build settings setting that I have not been able to identify. What I do is i clone this project: https://github.com/shindyu/XCTestExtensions And then clean it up and use it as a base. You can also rename the project etc. To find the magic setting, we can diff the .proj file. But I was not able to find the missing piece as of now.
- ⚠️️ (Optional-step) ⚠️️ Use `Cartfile.private` file instead of `Cartfile` because these dependencies will only be needed in this local project. And does not need to be visible for other dependencies higher up the hierarchy

## Nesting UI-Testing frameworks
- Use the same methodology as Non-nested UI-testing frameworks.

## Partial carthage solution: (for the sake of reference, dont use this)
- Add XCTest.framework in the linked framework and libraries list in General.
- You have to add Framework search-path: `$(PLATFORM_DIR)/Developer/Library/Frameworks` in Build settings
- import `MyTestFramework`
- Limitations: ⚠️️ I have not been able to load this kind of framework as a pure .framework through carthage, seems the .framework cant find the correct paths somehow. But you can load the src code from carthage and then build the .framework from that src code. Its a bit more hassle to manage src files, but with some more digging I think its possible to figure out the path issue

### Resources:
- UITest with languages: [https://github.com/PGSSoft/AutoMate](https://github.com/PGSSoft/AutoMate)

### Gotchas:
- Some limitations concerning Helper methods: [https://stackoverflow.com/questions/40184109/how-can-xctestcase-extensions-that-are-called-from-a-helper-method-know-what-pro](https://stackoverflow.com/questions/40184109/how-can-xctestcase-extensions-that-are-called-from-a-helper-method-know-what-pro)
