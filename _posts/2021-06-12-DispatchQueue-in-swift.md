My notes on DispatchQueue in swift<!--more-->

### Examples:
```swift
DispatchQueue.main.async {
   sleep(2) // do something on the main thread
}
DispatchQueue.global(qos: .background).async {
   sleep(2) // do something on a background thread
}

```
### DispatchQoS Priority Levels:
- **.userInteractive:** Used for work directly involved in providing an interactive UI
- **.userInitiated:** Used for performing work that has been explicitly requested by the user
- **.default:** This QoS is not intended to be used by developers to classify work.
- **.utility:** Used for performing work which the user is unlikely to be immediately waiting for the results.
- **.background:** Used for work that is not user initiated or visible.
- **.unspecified**

### Priority levels more deeply explained:
**.UserInteractive:** UserInteractive QoS is used for work directly involved in providing an interactive UI such as processing events or drawing to the screen.
**.UserInitiated:** UserInitiated QoS is used for performing work that has been explicitly requested by the user and for which results must be immediately presented in order to allow for further user interaction. For example, loading an email after a user has selected it in a message list.
**.Utility:** Utility QoS is used for performing work which the user is unlikely to be immediately waiting for the results. This work may have been requested by the user or initiated automatically, does not prevent the user from further interaction, often operates at user-visible timescales and may have its progress indicated to the user by a non-modal progress indicator. This work will run in an energy-efficient manner, in deference to higher QoS work when resources are constrained. For example, periodic content updates or bulk file operations such as media import.
**.Background:** Background QoS is used for work that is not user initiated or visible. In general, a user is unaware that this work is even happening and it will run in the most efficient manner while giving the most deference to higher QoS work. For example, pre-fetching content, search indexing, backups, and syncing of data with external systems.
**.Default:** Default QoS indicates the absence of QoS information. Whenever possible QoS information will be inferred from other sources. If such inference is not possible, a QoS between UserInitiated and Utility will be used.

**.userInteractive** is intended for user-interactive tasks, such as animations, events, and updating UI. As a rule of thumb, use this QoS for tasks that your app’s user is actively using.
**.userInitiated** is intended for tasks that prevent the user from using your app, like saving a file. Use it for tasks that your app’s user is actively waiting on.
**.utility** is intended for tasks that don’t require an immediate result, such as background downloads with a progress bar. Use it for background tasks that need a balance between responsiveness, performance and energy efficiency.
**.background** is intended for tasks that aren’t visible to your app’s user, like indexing, sync and backups. This setting prioritizes energy efficiency.

## A lot of good tips:
- https://gist.github.com/FWEugene/3861f0460c3e23f684e113f0f8d6947f
- https://medium.com/@engsultan2009/concurrent-management-in-ios-part-1-836b5b5da52f
