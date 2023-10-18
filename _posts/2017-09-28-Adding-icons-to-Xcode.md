My notes on how you can create and add an `.icns` to your xcode mac app project<!--more-->

### Xcode 8.2.1

1. Drag a 1024x1024 sized icon.png to [IconMaker](http://eon.codes/blog/2016/12/06/Creating-an-app-icon/) in order to make a .icns file
2. Add the `AwesomeApp.icns` file in the same folder as info.plist
3. Drag `AwesomeApp.icns` file into xcode in the same folder as `info.plist`
4. In info.plist set `icon`: to `AwesomeApp.icns`
5. Select the `AwesomeApp.icns` and in the Utilities make it target the app
6. Clean the project  (shift + cmd + k)
7. Run the app (cmd + r)

### Resources:
[Converting .png to .icns](http://eon.codes/blog/2016/12/06/Creating-an-app-icon/)
