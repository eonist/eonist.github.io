My notes on Static properties in swift 6.0<!--more-->These changes are to prevent data races and ensure your code is concurrency-safe in Swift 6

> TL;DR Swift 6.0 doesn't like `static let` solution use `static var`

### Problem:
- In Swift 6 with strict concurrency checking enabled, accessing static properties that may have shared mutable state is flagged as unsafe
- Upgrading to swift 6.0 Sometimes will throw errors in your consol: `Static property 'shared' is not concurrency-safe because non-'Sendable' type 'BLCentral' may have shared mutable state`
- This warning occurs because static properties are accessible from anywhere in the program, including concurrent contexts like background threads
- Concurrency safety refers to code that can safely execute in parallel without causing data races or unexpected behavior

### Solution:

The best approach depends on your specific use case:
- If the class has no mutable state, make it Sendable.
- If it's UI-related, use @MainActor.
- If it needs custom isolation, use a global actor.
- Only use nonisolated(unsafe) if you're sure about thread safety and can't use other methods.

### Options:

#### 1. Same as before:
- Use nonisolated(unsafe) as a last resort:
- Disable concurrency-safety checks if accesses are protected by an external synchronization mechanism
- This allows opting out of concurrency checking when necessary, but requires careful handling to avoid data races
- Only use this if you're absolutely certain the shared instance is safe and you can't use other methods
- Use nonisolated(unsafe) attribute (Swift 5.10+):

```swift
// We've now indicated to the compiler we're taking responsibility ourselves
// regarding thread-safety access of this global variable.
nonisolated(unsafe) static var shared: ImageCache!
```

#### 2. Use actor isolation (Thread safe):
- Annotate 'shared' with '@MainActor' if property should only be accessed from the main actor
- If the shared property and its usage are limited to the main thread, annotate it with @MainActor.
- This code will only be available form main thread
- This serializes access to the shared instance on the main actor, making it concurrency-safe

```swift
// Isolated by the @MainActor, making is concurrency-safe.
@MainActor class ImageCache {
    static let shared = ImageCache()
}
```

#### 3. Sendable:
- Make the type conform to Sendable explicitly, which signals that it can be safely shared across threads.
- This makes the class thread-safe by preventing mutable state
- We’ve marked the class as final, making introducing mutable states through inheritance impossible. This is required to make a class conform to Sendable.
- The shared variable is no longer mutable since we’ve defined it as a static let.

```swift
// The `MyClass` is `final` and conforms to `Sendable`, making it thread-safe.
final class MyClass: Sendable {
  // The global variable is no longer a `var`, making it immutable.
  static let shared = MyClass()
  // Ensure all properties are immutable or Sendable
}
```

#### 4. @TaskLocal

```swift
import Foundation

enum MyTaskLocal {
    @TaskLocal static var sharedResource: MySharedResource?
}

// Usage
Task {
    MyTaskLocal.sharedResource = MySharedResource()
    await doSomethingWithSharedResource()
}
```

#### 5. Explicit Synchronization

```swift
import Foundation

final class MySharedResource {
    private var _data: [String] = []
    private let lock = NSLock()
    var data: [String] {
        lock.lock()
        defer { lock.unlock() }
        return _data
    }
    func addData(_ newData: String) {
        lock.lock()
        defer { lock.unlock() }
        _data.append(newData)
    }
    static let shared = MySharedResource()
}
```

#### 6. Global actor
This isolates the class to a custom actor, providing concurrency safety
```swift
@globalActor actor MyActor { static let shared = MyActor() }

@MyActor
class MyClass {
  static let shared = MyClass()
}
```

### 7. Unchecked sendable:
The Easy Way Out: @unchecked Sendable

When should you use it, then? When you know what you are doing.

- You are 100% certain the class will never have a mutable state.
- You use more complex synchronisation methods that Swift's type system cannot automatically check.
```swift
final class MyClass: @unchecked Sendable {
  static let shared = MyClass()
}
```

## General Tips:
- Audit Your Type: Look at all properties and methods of the type. Ensure that mutable state is properly synchronized or avoided.
- Use @unchecked Sendable Sparingly: Use this only when you're certain the type is thread-safe but cannot automatically conform to Sendable.
- Avoid Overusing Singletons: They often lead to shared mutable state issues. Dependency injection is often a better alternative.
- Test Concurrency: Use tools like Xcode's thread sanitizer (TSAN) to catch potential race conditions or thread safety violations.

## Best Practices:
- Prefer actor isolation or Sendable conformance over using @unchecked Sendable 2.
- Consider refactoring global state into properties of specific types rather than standalone classes 3.
- When using manual synchronization like locks, ensure proper threading safety 3.
- For force-unwrapped globals that can't be easily rewritten, use nonisolated(unsafe) as a last resort 5.

## Gotchas: 
- `nonisolated(unsafe)` is not compatible with property wrappers
- `@MainActor` is not compatible with property wrappers

## Summary:
Addressing concurrency-safe global variables requires careful consideration of threading safety. Actor isolation, immutable types conforming to Sendable, or carefully managed manual synchronization are generally preferred over simply using @unchecked Sendable. The choice depends on your specific use case and requirements for thread-safety.

## Resources:
- [https://www.avanderlee.com/concurrency/concurrency-safe-global-variables-to-prevent-data-races/](https://www.avanderlee.com/concurrency/concurrency-safe-global-variables-to-prevent-data-races/) 
- [https://www.avanderlee.com/swift/race-condition-vs-data-race/](https://www.avanderlee.com/swift/race-condition-vs-data-race/) 
- [https://forums.swift.org/t/static-property-is-not-concurrency-safe-is-there-an-easy-solution/74983/2](https://forums.swift.org/t/static-property-is-not-concurrency-safe-is-there-an-easy-solution/74983/2) 
- [https://www.rudrank.com/exploring-swift-static-property-shared-is-not-concurrency-safe-because-non-sendable-type-may-have-shared-mutable-state/](https://www.rudrank.com/exploring-swift-static-property-shared-is-not-concurrency-safe-because-non-sendable-type-may-have-shared-mutable-state/) 