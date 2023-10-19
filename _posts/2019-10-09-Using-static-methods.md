My notes on static methods<!--more-->

### Why use static methods:
- If a private method can be static, it should be.
- The static marker provides important knowledge about the method: it doesn't deal with the object's state and doesn't call any other instance methods.
- If a public method satisfies the criteria to become static, it likely doesn't belong to the class it's in and should be relocated to a static utility class.
- Pure functions, such as `Character.toUpperCase(char)`, are good candidates for static methods.
- Using fewer instanced methods and variables can improve code quality.
- Modifying the value of an object from within a method is a side effect that can cause issues for other programmers on the team or even for yourself in the future.
- Static methods can be useful in microservices.

## Gotchas:
- Avoid using impure static methods that have side effects; any side effect calls should be swappable for testing. It's okay to create impure static methods, but it's a dangerous idea since it's less obvious how to factor those method dependencies out for swappability.
- Keep your own static methods short to maintain high testability.
- If you use a static method to manipulate the instance variables of an instance, be mindful of how many interactions it makes. If there are too many, it might be better to use a non-static method and find other ways to reduce complexity.
