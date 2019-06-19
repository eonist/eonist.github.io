My notes on setting up a UI-testing-framework<!--more-->

## To create a UI-test-framework you have to:
- Add XCTest.framework in the linked framework and libraries list in General.
- You have to add Framework searchpath: `$(PLATFORM_DIR)/Developer/Library/Frameworks` in Build settings
- import `MyTestFramework`
- Limitations: ⚠️️ I have not been able to load this kind of framework as a pure .framework through carthage, seems the .framework cant find the correct paths somehow. But you can load the src code from carthage and then build the .framework from that src code. Its a bit more hassle to manage src files, but with some more digging I think its possible to figure out the path issue

## 100% Carthage support
- Add the UITestingSugar.framework to the project folder then drag it to the frameworks folder. Then select the UITestTarget
- In The UITestTarget  -> build-phases -> + -> Copy files -> drag the .framework file into that panel
- Use `Cartfile.private` file instead of `Cartfile` because these dependencies will only be needed in this local project. And does not need to be visible for other dependencies higher up the hierarchy ⚠️️ (Optional-step) ⚠️️


### Resources:
- UITest with languages: [https://github.com/PGSSoft/AutoMate](https://github.com/PGSSoft/AutoMate)
