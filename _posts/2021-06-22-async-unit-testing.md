My notes on async unit testing <!--more-->

- The Expectations pattern is based on the usage of XCTestExpectation.
- Expectation is an expected outcome in an asynchronous test.

### The pattern can be summarized into four steps:
- Create an instance of XCTestExpectation
- Fulfil the expectation when async operation has finished
- Wait for the expectation to be fulfilled
- Assert the expected result

### Callback based Async unit tests:
```swift
// simple example:
func testAsyncFunction() {
    let asyncDone = self.expectation(description: "Async function") // expectation is in the XCTestCase
    // ...
    someAsyncFunction(...) {
        // ...
        asyncDone.fulfill() // call this to indicate the test was successful
    }
    wait(for: [asyncDone], timeout: 10) // Add after work has been called
    /* Test the results here */
}
```

### Inverted Expectations
Inverted Expectations allows us to verify that something did not happen

### Todo:
- look into: XCTNSPredicateExpectation
