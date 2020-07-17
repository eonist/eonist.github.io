My notes when you need to nuke xcode<!--more-->

1. Go to xcode -> file -> Project settings
2. Click the arrow next to which appears /Users/apple/Library/Developer/Xcode/DerivedData
3. Select the Derived data and move it to Trash. Terminal: `rm -rf ~/Library/Developer/Xcode/DerivedData`
4. Quite the xcode and reopen it.
5. Clean the project and run again.

Above steps should give you a clean xcode.
