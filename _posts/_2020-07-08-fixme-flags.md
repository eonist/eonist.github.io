My notes on adding fixme flags to your code in order to prioritize important tasks first <!--more-->

## Example:
With different warning flag counts, you are able to search your code-base for crucial and non essential fixes.
- 3 Warning flags means the task should be prioritized
- 2 warning flags means fix it soon
- 1 warning flag means it should be fixed, but might also be skipped as code matures etc, but keep it around

```swift
/**
 * Fixme: ⚠️️⚠️️⚠️️ add doc to this method
 */
func helloWorld() {
	// Fixme: ⚠️️remove Swift. because not needed in iOS
   Swift.print("swift is fun 🎉")
	// Fixme: ⚠️️⚠️️ rather throw a proper error
	fatalError("uh oh")
}
```
