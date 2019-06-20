My notes on UI-testing in Xcode <!--more-->

### iOS UI-testing
1. Add UITesting to your XCode project when you setup the XCode project
2. `XCUIApplication().launch()`
3. `app.otherElements.children(matching: .button).element.tap()`
4. Set your cursor inside a test method and click the red record button in the right corner of xcode,
5. click around the app to generate programatic UI-method calls
6. Create a check: `XCTAssertEqual(app.tables.cells.count, 56, "There should be 56 words matching 'test'")` etc

## Gotchas:
- Internal methods will run automatically in the test. Make them private to run them from setup.
- Prefer using: `waitForExistence(timeout:)` over a regular `exists` check
- Prefer using `firstMatch` over `element`

## Terminology:
- **XCUIApplication:** This class responsible for launching, terminating apps.
- **XCUIElement:** This class used to determine the UI element in the app and able to perform any action on it e.g tap, double tap, swipe etc
- **XCUIElementQuery:** This class can be used to uniquely identify the UI element on the screen.
We can form the query to uniquely identify the UI element on the screen. Imagine that you have to select the first button on the app. We can form the query like this XCUIApplication(). `buttons.element(boundBy: 0)`


## Pro's:
- Great way to make Unit-tests that matters.

## Con's
- Careful to not go overboard by testing everything, as that will make the app harder to develop on later

## Resources:
- A basic run-through of UI-testing: [https://medium.com/exploring-ios/an-introduction-to-ui-testing-on-ios-697526fa0fa9](https://medium.com/exploring-ios/an-introduction-to-ui-testing-on-ios-697526fa0fa9)
- Commonly used UI-Test method calls: [https://www.hackingwithswift.com/articles/148/xcode-ui-testing-cheat-sheet](https://www.hackingwithswift.com/articles/148/xcode-ui-testing-cheat-sheet)
- Properties of XCUIElement: [https://metova.com/a-guide-to-xcode-ui-test/](https://metova.com/a-guide-to-xcode-ui-test/)
- Cheat sheet on github: [https://github.com/joemasilotti/UI-Testing-Cheat-Sheet](https://github.com/joemasilotti/UI-Testing-Cheat-Sheet)
- TestExtension with lots of reusable code: (also testing languages) [https://cocoapods.org/pods/AutoMate](https://cocoapods.org/pods/AutoMate) 
