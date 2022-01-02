My notes on feature sandboxing<!--more-->

## Feature sandbox
A feature sandbox is a place within your app where you can quickly spike out implementations of new features without worrying about taking the time to implement a pretty user interface. I prefer to do this by creating a “Developer” screen where you can plop down buttons and display device raw device state. This screen is only available to engineers, developers, and testers.

Oftentimes, the device your app is communicating with is being developed at the same time as your app. The developers of the device will likely need some way of quickly testing new features that they are working on. Generic Bluetooth apps like LightBlue Explorer can be useful for testing simple things, but eventually, testers will need something more. If you can quickly build a feature that allows them to test more effectively, you will probably end up saving yourself a lot of time in the long run.
