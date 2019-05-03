My notes on UI-testing in Xcode <!--more-->

### iOS UI-testing
1. Add UITesting to your XCode project when you setup the XCode project
2.  `XCUIApplication().launch()`
3. `app.otherElements.children(matching: .button).element.tap()`


## Gotchas:
- Internal methods will run automatically in the test. Make them private to run them from setup.

## Pro's:

## Con's

## Resources:
- A basic run-through of UI-testing: [https://medium.com/exploring-ios/an-introduction-to-ui-testing-on-ios-697526fa0fa9](https://medium.com/exploring-ios/an-introduction-to-ui-testing-on-ios-697526fa0fa9)
