My notes on static methods<!--more-->

### Why use static methods:
- A private method should always be static if it can
- In the split-second it takes to notice the static marker you will have gained the following important knowledge: the method doesn't deal with the object's state and doesn't call any other instance methods.
- If a public method satisfies the criteria to become static, then it quite likely doesn't even belong to the class it's in because it has no coupling to it. In most cases such a method is a candidate for relocation into a static utility class.
- `Character.toUpperCase(char)` —a pure function, performs a simple translation
- You get a higher score in code quality services if you use less instanced methods and variables
- Modifying the value of an object from within a method is a side effect. As long as you are aware of the side effect, everything is O.K., but when another programmer joins the team, they aren't aware of the side effect and runs into trouble. It may even happen to yourself when you return to the project after a year or two.
- The future is microservices


## Gotchas:

- Whatever you do, don’t directly use static methods that are impure, that have side effects; any side effect calls should be swappable for testing. It’s slightly okay to create impure static methods, though it’s a dangerous idea, since it’s less obvious how to factor those method dependencies out for swappability.

- When making our own static methods, keep them short, like anything else; this helps to keep the testability of the methods high.

- If you use a static method to manipulate the instance variables of an instance, be mindful about how many interactions it makes, too many and it might be better to use a non static method. And figure out other ways to reduce complexity.
