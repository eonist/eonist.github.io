My notes on queuing tasks up and making things concurrent in swift <!--more-->

- [https://eon.codes/blog/2016/12/23/Background-thread.md](https://eon.codes/blog/2016/12/23/Background-thread.md)
- [https://eon.codes/blog/2021/06/12/DispatchQueue-in-swift.md](https://eon.codes/blog/2021/06/12/DispatchQueue-in-swift.md)
- [https://eon.codes/blog/2017/04/03/Dispatch-group.md](https://eon.codes/blog/2017/04/03/Dispatch-group.md)
- [https://eon.codes/blog/2021/06/10/NSOperationQueue-in-swift.md](https://eon.codes/blog/2021/06/10/NSOperationQueue-in-swift.md)
- [https://eon.codes/blog/2020/07/08/how-to-do-concurrency-in-swift.md](https://eon.codes/blog/2020/07/08/how-to-do-concurrency-in-swift.md)
- [https://eon.codes/blog/2019/12/21/Concurrent-perform.md](https://eon.codes/blog/2019/12/21/Concurrent-perform.md)
- [https://eon.codes/blog/2019/10/14/Semaphores.md](https://eon.codes/blog/2019/10/14/Semaphores.md)
- [https://eon.codes/blog/2017/02/26/swift-3-threading.md](https://eon.codes/blog/2017/02/26/swift-3-threading.md)
- [https://github.com/eonist/ParallelLoop](https://github.com/eonist/ParallelLoop)

### Overview:

**DispatchQueue**
- Call items async or sync
- Create dependencies with callbacks
- Call things on the main thread or in background thread

**DispatchGroup**:
- Launch many operations async or serial and attach a completionBlock when they all complete
- If one operation fail, you have to call .leave. or else complete is never called, you can use .wait for group timeout

**DispatchWorkItem**:
- Ability to cancel many tasks in the queue
- Simpler than NSOperationQueue
- Has ability to wait (side effect is that it block the thread)
- Can be built for Search trotting functionality for instance

**NSOperationQueue**:
- More complex than DispatchWorkItem (can be a good idea to start with DispatchWorkItem before advancing to NSOperation)
- Ability to cancel single or all operations in a queue
- Ability to make one operation dependant on the completion of another
- Ability to override and create subclasses
- More control over different states the items has
- Can set priorities and assign different queues
- Can nest Operation queues in a multi-dimensional structure
- Can run one task after the previous finished etc (or all at the same time)

**Concurrent perform**:
- Great for managing many small cpu intensive tasks, and dividing tasks efficiently to threads
- Can be used for async network calls if semaphore is used
- Can be used with AtomicValue (thread-safe variable) (see ParallelLoop project)
- Can do Any combination of: sync, async, concurrent, serial

**Semaphores**:
- Semaphores has the ability to convert-a-callback based method into a returning-style-method. ✨
- Semaphores also has the ability to timeout similarly to `dispatchgroup`

**Todo**:
- write about async UnitTesting in xCode (or ref to the one you wrote before)
