Creating a Programatic NSMenu <!--more--> 

### Conceptually:

1. The NSMenu class can hold NSMenuItems
2. To add an item to an NSMenu you need to First add an NSMenuItem to the supermenu. And then set the submenu of this to NSMenu()
3. You can set title and key trigger and also delegate an action to a method. A target value can be set to scope the delegation

### Example:

```swift
//coming soon
```

**to enable s + ctrl key combo:**
```swift
keyEquivalentModifierMask = NSEventModifierFlags.ControlKeyMask.rawValue.int
```