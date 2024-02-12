My notes on using an actor in swift<!--more-->

### Actors in Swift are like safe boxes for your data:

- They keep your data safe from being changed by different parts of your program at the same time.
- They make sure that only one part of your program can change the data at a time.
- This stops any mistakes from happening when your data is being used by different parts of your program at the same time.


### Actors in Swift have several benefits:

- They are safe: They make sure that your data is always correct, even when used in different parts of your program at the same time.
- They are fast: They are designed to work well when many things are happening at once.
- They make your code easier to read: They help you organize your code in a way that makes sense.
- They are easy to use: They handle the hard parts of making your code safe, so you can focus on what your code should do.

### Actors in Swift have three main features:

- Isolation: Each actor works on its own and doesn't interfere with others.
- One thing at a time: Inside an actor, only one thing can happen at a time.
- Safe messaging: Actors talk to each other using messages. This is done in a way that is safe and predictable.

### How to Use Actors in Swift
In the example below, BankAccount is a special type of object that manages its own balance. The methods deposit, withdraw, and getBalance need to be called with 'await'. This makes sure that these methods are called one after the other in a safe way.

###  Define an actor
```swift
// Define an actor
actor SavingsAccount {
    private var funds: Double = 0.0
    // An asynchronous method to add money
    func add(amount: Double) {
        funds += amount
    }
    // An asynchronous method to safely remove money
    func remove(amount: Double) -> Bool {
        if funds >= amount {
            funds -= amount
            return true
        } else {
            return false
        }
    }
    // A method to check the current funds
    func checkFunds() -> Double {
        return funds
    }
}

```
### Using an actor
```swift
// Implementing the actor in your code
func executeFinancialTransactions(savingsAccount: SavingsAccount) async {
    await savingsAccount.add(amount: 100.00)
    let transactionStatus = await savingsAccount.remove(amount: 50.00)
    print(transactionStatus ? "Transaction successful" : "Transaction failed")
    let currentFunds = await savingsAccount.checkFunds()
    print("Current funds: \(currentFunds)")
}

// Creating and interacting with an actor
let savingsAccount = SavingsAccount()
Task {
    await executeFinancialTransactions(savingsAccount: savingsAccount)
}
```

The actor in Swift is a powerful tool for writing concurrent code that’s both safe and relatively simple. It encapsulates state and behaviour, ensuring that data is accessed in a controlled manner.

### Resources:
- https://medium.com/@valentinjahanmanesh/swift-actors-in-depth-19c8b3dbd85a
- more examples: https://medium.com/@bharathi.r/actor-in-swift-a1b63ae27eb1