My notes on keyboard managmenet for swiftui<!--more-->

### Gotchas:
- Reset prevew simulator: `xcrun simctl --set ~/Library/Developer/Xcode/UserData/Previews/Simulator\ Devices shutdown all` ref: https://stackoverflow.com/a/67100729/5389500

### Resources
- https://www.vadimbulavin.com/how-to-move-swiftui-view-when-keyboard-covers-text-field/
- https://stackoverflow.com/questions/56491881/move-textfield-up-when-the-keyboard-has-appeared-in-swiftui
- https://stackoverflow.com/questions/28813339/move-a-view-up-only-when-the-keyboard-covers-an-input-field
- Dimissing focus: https://stackoverflow.com/questions/58987542/how-can-i-remove-textfield-focus-when-i-press-return-or-click-outside-textfield?rq=3
- lots of nuances on safearea: https://fatbobman.medium.com/mastering-safe-area-in-swiftui-a183b8ad04d0