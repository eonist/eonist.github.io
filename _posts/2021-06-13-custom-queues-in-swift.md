My notes on making a queue in swift <!--more-->

### Queue concept:
- A queue is a list where you can only insert new items at the back and remove items from the front. This ensures that the first item you enqueue is also the first item you dequeue. First come, first serve!
- A Queue fits quite well any use-cases where the information needs to be computed in the specific entry order. For instance, if you build a chat interface, you want to include each message in the same way they have been typed. Queueing them in the same order while sent to a backend service would be a nice way to do it.

### Queue:
Queue system that doesn't use operations:
- Enqueue inserts an element to the back of the queue.
- Dequeue removes the element at the front of the queue.

**String example:**
```swift
struct Queue {
    var items: [String] = []
    mutating func enqueue(element: String) {
        items.append(element)
    }
    mutating func dequeue() -> String? {
        if items.isEmpty { return nil }
        else {
            let tempElement = items.first
            items.remove(at: 0)
            return tempElement
        }
    }
}
```

**Generic example:**
```swift
struct Queue<T> {
  private var elements: [T] = []
  // Add
  mutating func enqueue(_ value: T) {
    elements.append(value)
  }
  // Remove
  mutating func dequeue() -> T? {
    guard !elements.isEmpty else { return nil }
    return elements.removeFirst()
  }
  // First
  var head: T? {
    return elements.first
  }
  // Last
  var tail: T? {
     return elements.last
  }
  public var isEmpty: Bool {
     return list.isEmpty
  }
}
// Example code:
let queue = Queue<String>()
queue.enqueue("Adam")
queue.enqueue("Julia")
queue.enqueue("Ben")
// We have 3 customers to serve, we're going to serve them in order of arrived
let serving = queue.dequeue() // Adam
let nextToServe = queue.head // Julia
```

### Double Ended Queue
A Double Ended Queue lets you add and remove elements at both ends of the queue. That means besides the enqueue, dequeue functions, you need to add two additional equeueFront and dequeueBack functions too.
Following code shows a Double Ended Queue structure using an Array.
```swift
public struct Deque<T> {
    private var items = [T]()
    mutating func enqueue(element: T) {
        items.append(element)
    }
    mutating func enqueueFront(element: T) {
        items.insert(element, at: 0)
    }
    mutating func dequeue() -> T?{
        if items.isEmpty {
            return nil
        } else {
            return items.removeFirst()
        }
    }
    mutating func dequeueBack() -> T? {
        if items.isEmpty {
            return nil
        } else {
            return items.removeLast()
        }
    }
}
```
- In the above illustration, enqueueFront adds the new element before the current one. deuqueBack() deletes the last element from the queue.
- This brings an end to this quick tutorial on Swift Queue data structure implementation.

### References:
- Algo club queue: [https://github.com/raywenderlich/swift-algorithm-club/tree/master/Queue](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Queue)
- Stack is a LIFO data structure: [https://www.journaldev.com/21287/swift-stack-implementation](https://www.journaldev.com/21287/swift-stack-implementation)  
- Complexe queues in swift: [https://www.raywenderlich.com/books/data-structures-algorithms-in-swift/v3.0/chapters/8-queues (paid article)](https://www.raywenderlich.com/books/data-structures-algorithms-in-swift/v3.0/chapters/8-queues (paid article))
