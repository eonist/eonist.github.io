My notes on UI-testing in Xcode <!--more-->

### iOS UI-testing
1. Add UITesting to your XCode project when you setup the XCode project
2. `XCUIApplication().launch()`
3. `app.otherElements.children(matching: .button).element.tap()`
4. Set your cursor inside a test method and click the red record button in the right corner of xcode,
5. click around the app to generate programatic UI-method calls
6. Create a check: `XCTAssertEqual(app.tables.cells.count, 56, "There should be 56 words matching 'test'")` etc

## Accessibility
- Use the Accessibility inspector to look at what your UI elements’ accessibility values are before you even need to run the test. Use this to make sure that the accessibilityLabel you assigned for your UI element is being found, or if the element is being considered an accessibility element at all. However, you’ll note that you can’t see the accessibilityIdentifier on this view. Nonetheless it is very useful especially to determine if your element is visible to accessibility in the first place. If you find you can’t highlight the view you’re looking for, chances are the superview is intercepting the accessibility focus, and you need to set its isAccessibilityElement to false.

## Gotchas:
- Internal methods will run automatically in the test. Make them private to run them from setup.
- Prefer using: `waitForExistence(timeout:)` over a regular `exists` check
- Prefer using `firstMatch` over `element`
- Open the `Accessibility Inspector.app` in macOS as a way of identifying accessibility ids.
- for IOS there is the:  `iOS Simulator's Accessibility Inspector`
- To access elements by accessibility ids: set/override: `accessibilityLabel`  with an id and set/override the `isAccessibilityElement` with true
- For some strange reason sometimes only `accessibilityIdentifier` works and you have to set the `isAccessibilityElement` setting or overide works
- Find accessibility elements by: `element.label == "someLabel"` if you are using accessibilityIdentifier or `element.identifier == "someId"` if you are using accessibilityLabel
- Use `XCUIElementQuery.debugDescription` to debug a query (Accessibility Hierarchy)
- ⚠️️IMPORTANT ⚠️️ Containers can have accessibilityIdentifier but they should have accessibility turned off. Logic is that we don't interact with containers, but we do need to access testing via accessibility hierarchy. setting isAccessibilityElement to true on a container will cause problems with UITesting. Setting it on leaf elements such as buttons etc is fine.
- Parsing a queries is much faster than parsing element

## Terminology:
- **XCUIApplication:** This class responsible for launching, terminating apps. ⚠️️ Not a singleton. ⚠️️
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
- video tut: [https://www.raywenderlich.com/3619-207-xcode-ui-testing](https://www.raywenderlich.com/3619-207-xcode-ui-testing)  
- video 2: [https://www.raywenderlich.com/3573-beginning-ios-unit-and-ui-testing/lessons/5](https://www.raywenderlich.com/3573-beginning-ios-unit-and-ui-testing/lessons/5)
- Working with webview and facebook: [https://hackernoon.com/running-uitests-with-facebook-login-in-ios-4ac998940c42](https://hackernoon.com/running-uitests-with-facebook-login-in-ios-4ac998940c42)
