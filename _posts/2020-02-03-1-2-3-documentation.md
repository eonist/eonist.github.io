My notes on writing method step documentation<!--more-->

### Introduction
- Software is complex.
- Some methods use many levels of closures, helpers, etc.
- Instead of putting the responsibility of reasoning on the reader, why not write step documentation in the method doc.
- This way you don't have to jump through a lot of files and methods and in the process lose your barrings
- The reader can easily just derive the context from reading the steps in the method doc

```swift
/**
 * Call remote service for the stockPrice
 * 1. Calls remote api
 * 2. Cleans up the result a bit
 * 3. Combines the result a bit
 * 4. Splits the result, because we only need the first part
 * 5. Return the result we are looking for
 */
func getStockPrice(url: String, stockName: String, onComplete: (String?) -> Void) {
    networkCall(url: url, stockName: stockName) { result
		 let processedResult = process(result)
		 let combinedResult = combine(processedResult)
		 let split = split(combinedResult)
		 onComplete(split.first)
	 }
}
```
