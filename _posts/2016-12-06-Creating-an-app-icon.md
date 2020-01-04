Creating an app icon that can be use for macOS apps<!--more--> It also supports retina screens.

### .png 👉 .icns
1. Download [IconMaker.app](https://raw.githubusercontent.com/stylekit/img/master/iconmaker.app.zip) 👈 (It's just an .applescript won't bite)   
2. If you get a warning while using the app go to `MacOS prefs -> security & privacy -> General -> click open anyway`
3. Drag and drop your .png onto the IconMaker.app and your done 🎉

### Example:
here is an app icon I created in Illustrator:  

<img width="314" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/gitsync_logo_2016_blue.png">

[gitsync_logo_2016_blue_1024.icns](https://raw.githubusercontent.com/stylekit/img/master/gitsync_logo_2016_blue_1024.icns.icns)

Now you have the .icns file that can be added to your xcode project.

### Build IconMaker.app your self:
This applescripts can convert a .png to an .icns file in **4 simple steps:**

1. Open the AppleScript app  
2. Copy and paste the code into AppleScript from JoeFrizzell in the link bellow.   
3. Save as: IconMaker.app   
4. Drag and drop your .png onto the IconMaker.app you just created.  

Credits to JoeFrizzell via: [http://apple.stackexchange.com/a/140361/179041](http://apple.stackexchange.com/a/140361/179041)


### Running the app requires:
MacOS Sierra has stricter security than before. To bring back running apps from unidentified developers simply write sudo spctl `--master-disable` in terminal and hit enter, as per: [http://osxdaily.com/2016/09/27/allow-apps-from-anywhere-macos-gatekeeper/](http://osxdaily.com/2016/09/27/allow-apps-from-anywhere-macos-gatekeeper/) (You can always enable it after) Or you can build from source your self and GateKeeper won't complain.


### High Sierra update:
Since macOS high sierra, you need to be strict about size. Make sure the png size is exactly 1024x1024px. Or else it won't work. Basically the `iconutil` that IconMaker utilises has been upgraded in High Sierra. I tested a 1000x1000px file in Sierra and it worked. But not in High Sierra. So make sure your source png is the correct size. 💪


### Doing all of this with just terminal:
1. Create a png file that is exactly 1024x1024px
2. Make sure all your icons are named exactly like the bellow: (it can be the same 1024x1024px png, but they must be named correct)
```
icon_16x16.png
icon_16x16@2x.png
icon_32x32.png
icon_32x32@2x.png
icon_128x128.png
icon_128x128@2x.png
icon_256x256.png
icon_256x256@2x.png
icon_512x512.png
icon_512x512@2x.png
```
3. Put the png's in a folder named: someIcon.iconset and put the folder on your desktop

4. Open Terminal and paste this line: `iconutil --convert icns ~/Desktop/someIcon.iconset`

5. You should now have someIcon.icns on your Desktop ready to be used in your macOS app projectgit


### Bonus points:
`sips -Z 512 ~/Desktop/someIcon.png`
You could add shell commands that resizes the images for you, in the applescript to actually resize each image. But I didn't have time for this step this time around. Maybe next time I revisit this app ill implement it. You can also probably make a photoshop script that does all the resizing and renaming for you. let me know if you make any of this ideas and ill be sure to share it with the world. Thanks for reading ✌️

### Resources:
- For ios11 icons you can use this online converter: [https://makeappicon.com](https://makeappicon.com)
- There is a sketch plugin that makes xcode icons [https://github.com/nathco/Export-More](https://github.com/nathco/Export-More)
