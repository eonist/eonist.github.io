My notes on Interface Builder for iOS <!--more-->

### Vector assets
- You can use vector graphics but you have to use .pdf since ios has no support for xml or svg. You add .pdf assets by draging them into the assets.casette
- Supposedly you can add an svg to a left bar nav button. But scaling it becomes a problem. A trick is to embedd the button in a UIView and add that to the left bar nav section. But then the button becomes unclickable. More research needed.

### Advance UI/UX animation:
very dribbble like UI style: takes a long time to code. and feels fragile. He is also an expert in IB and he struggles all the way through.
https://www.youtube.com/watch?v=MsNzYMMBIJ8&list=PLHDMmeIMXj8VRKB1w6L3igjFyhKi2hy9X&index=7

### Beginer IB project by apple:
https://developer.apple.com/library/content/referencelibrary/GettingStarted/DevelopiOSAppsSwift/BuildABasicUI.html

### Segue
Segue is essentially a way to get and set data from prev and next view . you can call `performSegue(withIdentifier:"nameOFSegueInIb",sender:self)` to transition to another view from swift code

## UIAlertController (alert dialog)
See 114 Spinners  Alerts.mp4 in complete-ios-10-developer-course

## Conclusion:
UIBuilder is rather simple. It's not much harder than using Applescript GUI. It consists of 3 parts. UI elements, Segues (aka transitions), outlets (refs in your code to the UI element) everything else is pretty trivial and solutions are a google search away. 