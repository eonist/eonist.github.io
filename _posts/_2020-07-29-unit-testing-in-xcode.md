# 10 Reasons to Write Unit Tests
- Tests Reduce Bugs in New Features.
- Tests Reduce Bugs in Existing Features.
- Tests Are Good Documentation. A concise code example is better than many paragraphs of documentation.
- Tests Reduce the Cost of Change.
- Tests Improve Design.
- Tests Allow Refactoring.
- Tests Constrain Features.
- Tests Defend Against Other Programmers.
- Testing Forces You to Slow Down and Think.
- Tests Reduce Fear One of the biggest fears that programmers encounter is making a change to a piece of code and not knowing what is going to break.

### Callback based Async unit tests:
```swift
func testAsyncFunction() {
    let asyncDone = expectation(description: "Async function")
    ...
    someAsyncFunction(...) {
        ...
        asyncDone.fulfill()
    }
    wait(for: [asyncDone], timeout: 10)
    /* Test the results here */
}
```
