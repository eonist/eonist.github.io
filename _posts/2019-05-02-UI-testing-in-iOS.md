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

## Pro's:
- Great way to make Unit-tests that matters.

## Con's
- Careful to not go overboard by testing everything, as that will make the app harder to develop on later

## Resources:
- A basic run-through of UI-testing: [https://medium.com/exploring-ios/an-introduction-to-ui-testing-on-ios-697526fa0fa9](https://medium.com/exploring-ios/an-introduction-to-ui-testing-on-ios-697526fa0fa9)
- Commonly used UI-Test method calls: [https://www.hackingwithswift.com/articles/148/xcode-ui-testing-cheat-sheet](https://www.hackingwithswift.com/articles/148/xcode-ui-testing-cheat-sheet)
