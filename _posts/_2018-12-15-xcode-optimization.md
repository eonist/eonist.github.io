<!--more-->

### Build times:

You can enable a timer right within Xcode’s UI. This timer is not visible by default but if you run the following in the command line a time will be displayed each time you build your app.

`defaults write com.apple.dt.Xcode ShowBuildOperationDuration -bool YES`

After you have enabled the timer, you will see the time it takes to compile your app in the build status bar in Xcode.

👉 Remember to clean your proj to show this build time 👈


### Build time details

`Product → Perform Action → Build with Timing Summaryoption.`
