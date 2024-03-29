 My preliminary notes on AppCode<!--more--> 

## Pros: ✅
- AppCode can rename symbolically 🏆. Meaning it can rename a function or class and globally change every reference in your entire codebase to the new name. This is worth the price of admission alone
- AppCode edits your xcode .xcodeproject file when moving files. Everything is perfectly mirrored. This is worth its weight in gold alone. My projects are going to be so organized. You can move files into xcode groups and the files actually go into their respective folders in finder. WOW
- Building an app works, .frameworks seems to work.
- MY entire complicated "Ultimate SPM workflow" worked out of the box just adding the project folder to AppCode
- It's fast. Unlike xcode
- Appcode asks permission to share app metrics anonymously. It's good that they ask and that it's not hidden in some obscure setting. Happened on the 8th run or so. 
- You can paint the selection like TextMate 🎨
- jetbrains CLion has support for swift now. Meaning in theory you can code swift on a linux machine. If only there was a cross platform UI library 🤔 We could start coding apps on Linux machines, that worked for mac and linux. Which means we could buy really fast computers on the cheap for desktop development: 8-core CPU 64GB ram, 1GB SSD for less than 2k$. Add a 5K LG screen to that rig please. The future is bright.	

## Cons: 🚫
- It feels like Eclipse. It's java.
- No emoji support 🙁 
- TextExpander doesn't work, will try to find a solution for this!
- XCode Tree-view collapses if you do something in AppCode. 
- Sometimes the AppCode icon jumps up and down with a green ✅ sign. I presume it's updated, but no info in the app 🤔
- Way too many preference options, which is often the case with Eclipse/java based apps. Less is more. Hide the advance stuff please. 80/20 rule? The prefs that are used a lot, highlight these then hide the prefs that isn't used much.
- you have to doubble click to open files from tree menu (possible changeable in settings)
- AppCode feels like a futuristic code editor. 
- The symbolic renaming functionality doesn't always work (sometimes it doesn't catch all reference in the code base)
- AppCode can't update inside AppCode, meaning you have to go to the website, download, then install etc. They offer something called ToolBox that can install updates. But this should be handled inside AppCode IMO
- When you press cmd + shift + o to access quick-find, irrelevant results are shown above relevant ones. Search for "SomeClass" and "SomeClass.d" is shown above "SomeClass.swift" b
## Top AppCode key-combos 🏄

- Your personal AppCode concierge: (cmd + shift + A) it basically list all available commands and is searchable. When you don't want to searh helplessly through the gazillion pref options just try to free associate  about what you want to do and things may show up here. Like bigger font size, write increase font etc. 
- CMD + SHIFT + O: Same as XCode. open file
- XCode 9 now has Symbolic renaming. 
- AppCode will have a hard time keeping up with XCode. The cost will be high. Which means it will be abandoned soonish. Which leaves the user with nothing. Code editors are becoming more like ecosystems and less like standalone apps

## AppCode concierge 💁
- "select in project" view 👉 shows you the active file in the file-tree-view (rather cumbersome shortcut combo: alt + f1  then enter) 