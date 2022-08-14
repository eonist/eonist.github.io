Notes on how to get around hurdles when trying to run an app from xCode on an iPhone<!--more-->

### Common hurdles:

**Problem**: "iPhone is busy: Fetching debug symbols for iPhone"  
**Solution**: Wait for 10-15 min. unpairing iphone from xxcode also can work. more solutions: [Stackoverflow](https://stackoverflow.com/a/47448911/5389500)  

**Problem**: "Phone is busy: Preparing Johns Apple Watch for development via iPhone"
**Solution**: set the apple watch to airplane-mode + turn of bluetooth on the watch before running more solutions: https://stackoverflow.com/questions/69517131/xcode-13-build-hangs-with-iphone-is-busy-making-apple-watch-ready-for-develop

**Problem**: iPhone not available - please reconnect the device
**Solution**: unplug iphone usb cable, restart xcode. Try again

**Problem**: The operation couldn’t be completed. Unable to launch some.app.com because it has an invalid code signature, inadequate entitlements or its profile has not been explicitly trusted by the user.
**Solution**: On iPhone settings -> device managment -> enable trust for the app / developer


**Problem**: Please delete apps signed with your free account from this device to remain under the limit.
**Solution**: free accounts can only have 3 apps. Delete one or use a paid dev account
