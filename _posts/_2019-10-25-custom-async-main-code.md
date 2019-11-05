


async_if_not_main:

```swift
* DispatchQueue.main.async {
*     print("1")
*     DispatchQueue.main.async {
*         print("2")
*     }
* }
* DispatchQueue.main.async {
*     print("3")
* }
* //1,3,2

```


- If you are on main thread there can be also good reasons to launch `DispatchQueue.main.async` to let finish some UI operations first and later execute something else.
- First of all I would recommend using `Thread.current.isMainThread` instead of: `Thread.current == Thread.main`, maybe second solution works too. ~However it’s not clear that we can do this by comparison of references.~ (comparison of references would be ===)
- Discussion why mainTraid != mainQueue: http://blog.benjamin-encz.de/post/main-queue-vs-main-thread/
- Another https://blog.krzyzanowskim.com/2016/06/03/queues-are-not-bound-to-any-specific-thread/
- An alternative approach to take is to let the controllers manage dispatch, normal functions should be void of dispatch — because of having multiple call sites. It makes your models cleaner, but also expects the person who’s writing the controller to be more mindful of dispatchs
- Main thread and main queue are not necessarily tied together
- Method name is misleading: If won't be async if you're on the main thread
- Personally, I prefer to use `DispatchQueue.main.async` because when the main queue is empty the main thread can be used outside the main queue to avoid context switching. So `Thread.current == Thread.main` guarantees you that you are in the main thread but not guarantee you that the code comes from the main queue.
- And yes I would just use regular main.async. I don’t like to managing my threads with different tools / at different levels. I will make sure that all my threads are managed by GCD. And generally all methods that must be executed on Main Thread / Main queue, I use `dispatchPrecondition(condition: DispatchPredicate.onQueue(.main))` as instruction. https://developer.apple.com/documentation/dispatch/1780605-dispatchprecondition
- You might not be on the main queue even though the thread is the one assigned to the main queue - your main queue can be idle (no events in the runloop) and because of that the thread might be reused in another queue
- I understand what this method is trying to achieve with, I'm just not sold that its doing what it wants to do (make the call be the next processor instruction instead of putting it at the end of current instruction block)
-  think about threads like reusable cells, and let’s say you only support that the first cell is responsible for UI changes now as you scroll down  the reusable cell is not needed for the first cell as it’s not on the screen - so it will be used on index row 24 as this is what you currently use when you do a check if the row 24 is the main cell, of course it will return true because it is, but it’s not the first cell currently and it’s not doing the work for the first, main cell, but for the 24th cell
- All the arguments aside. Im not claiming Im right here. I just think its not as simple as it seems. To me its unconventional and i might not use it when i implement a feature. I rather solve the same purpouse but with different toolings, methodologies.
- Also its a communication thing. This method may be the best ever. But its undocummented with no reference. And there is hardly any places online using similar code.
- What about docummenting what it tries to do, what to look out for and how it works. That way people that are sceptical to unconvention can get onboard quicker and start using it without having to understand everything.  The problem here is using unconventional methods blindly. Im always skeptical of that.
- Here is a quick stab at documenting its intent etc: (Totally WIP, and needs refinement, its just a suggestion)



```swift
/**
 * - Description: With this extension method, if the current thread is main `work()` will be executed immediately. Otherwise it will be added at the end of the main queue.
 * - Discussion: if you only use `DispatchQueue.main.async`, whatever you are on the main thread or not it will be added at the end of the main queue.
 * - Discussion: dispatching to main will put that block at the *end* of whatever other things the main queue has to get to...which is a generally a bad idea.
 * - Discussion: Use this method when a single function has multiple call sites. Some are from background and some where from main
 * ## Examples:
 * DispatchQueue.main.async {
 *     print("1")
 *     DispatchQueue.main.async {
 *         print("2")
 *     }
 * }
 * DispatchQueue.main.async {
 *     print("3")
 * }
 * //1,3,2 (but if you used asyncIfNot, it would print 1,2,3)
 */
func async_if_not_main() {
  //...
}
```
