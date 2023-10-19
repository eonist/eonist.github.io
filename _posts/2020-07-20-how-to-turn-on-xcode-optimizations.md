My notes on xcode optimizations<!--more-->

### Whole module
Whole module optimization is a compiler pass that can add significant performance gains, and so it's always worth enabling when doing a release build of your app for the App Store. How it works is quite simple: when Swift builds the final version of your app it combines all your source files together and can evaluate the whole structure of your program at once. This lets it make extra optimizations that would be impossible before, when every file was optimized individually.
- Set Compilation mode for debug to Whole module. (it made cpu speeds 2x) (could make compile time longer, so toggle this depending what the focus is, performance gains vs compile time)  
- More info: [https://gist.github.com/lsavino/38367f10c2d20aeec4f031610d2929b8](https://gist.github.com/lsavino/38367f10c2d20aeec4f031610d2929b8)
`Project target -> Build settings -> Compilation mode`

### Optimization level
- Set optimization level for debug to fastest, smallest (might want to toggle this if your not performance testing etc)
`Project target -> Build settings -> Optimization level`

### Build times:
You can enable a timer right within Xcode’s UI. This timer is not visible by default but if you run the following in the command line a time will be displayed each time you build your app.

`defaults write com.apple.dt.Xcode ShowBuildOperationDuration -bool YES`

After you have enabled the timer, you will see the time it takes to compile your app in the build status bar in Xcode.

👉 Remember to clean your xcode project to show this build time 👈

### Build time details
`Product → Perform Action → Build with Timing Summary option.`

## Terminal:
Performance regarding swift package manager tests

To test with optimization levels turned on: `swift test -Xswiftc -O` and type --help for other flags to use
- Ref: [https://stackoverflow.com/questions/39775937/how-to-build-optimized-version-of-swift-package-using-the-swift-package-manager](https://stackoverflow.com/questions/39775937/how-to-build-optimized-version-of-swift-package-using-the-swift-package-manager)


### other:
- add dealloc checker lib: [https://github.com/fastred/DeallocationChecker](https://github.com/fastred/DeallocationChecker)
