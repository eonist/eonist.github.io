My notes on xcode optimizations<!--more-->

### Whole module
- Set Compilation mode for debug to Whole module. (it made cpu speeds 2x) (could make compile time longer, so toggle this depending what the focus is, performance gains vs compile time)  
`Project target -> Build settings -> Compilation mode`

### Optimization level
- Set optimization level for debug to fastest, smallest (might want to toggle this if your not performance testing etc)
`Project target -> Build settings -> Optimization level`

### Build times:
You can enable a timer right within Xcode’s UI. This timer is not visible by default but if you run the following in the command line a time will be displayed each time you build your app.

`defaults write com.apple.dt.Xcode ShowBuildOperationDuration -bool YES`

After you have enabled the timer, you will see the time it takes to compile your app in the build status bar in Xcode.

👉 Remember to clean your proj to show this build time 👈

### Build time details
`Product → Perform Action → Build with Timing Summary option.`
