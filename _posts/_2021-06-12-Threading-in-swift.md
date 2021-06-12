My notes on making things async in swift <!--more-->

### DispatchGroup:
- Launch many operations async or serial and attach a completionBlock when they all complete
- If one operation fail, you have to call .leave. or else complete is never called, you can use .wait for group timeout

### DispatchWorkItem
- Ability to cancel tasks in the queue
- Simpler than NsOperationQueue
- Can be used for Search trotting for instance

### NSOperationQueue
- More complex than DispatchWorkItem
- Ability to cancel single or all operations in a queue
- Ability to make one operation dependant on the completion of another

### References:
2017-04-03-Dispatch-group
2021-06-10-NSOperationQueue-in-swift
