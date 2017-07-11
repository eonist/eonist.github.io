Searching for a Gif animation app that can output crisp .gif animations of GUI animation examples. <!--more-->  Maybe there is a way to  automate gif creation with photoshop and applescript. ([Gif brewery](gifbrewery.com/)  cant output crisp GUI animation, the drop-shadow isn't good etc, [gifrocket](http://www.gifrocket.com)   looks promising, the drop-shadow works, but the frame-rate is really slow somehow. Then there is [giphy](http://www.giphy.com)  Which I currently use, the problem is that there is no quality settings or size settings, the size is also resized to something different than the original.
	

### Here is what I want:
1. smooth drop-shadow 
2. high-res (so that it looks great on Retina screens) 
3. 1 to 1 frame-rate
4. ==files size doesn't matter==, since twitter[^1] converts all .gif files to .mov files in the backend anyway.

### Solution:
==Well that was easy.== Did another sweep on google and it turns out photoshop can import .mov files. And export to .gif animations. It has an entire suit of tools that includes timeline different compression settings etc.

1. Open .mov in Photoshop[^2] (a timeline at the bottom will show up)
2. If you are happy with the timeline settings, click file -> save for web
3. Adjust the quality and size settings to your likings and then hit save. Thats it.

### Result:

<img width="442" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/hover_svg_asset_d8jd.gif">

Retina-ready and Perfect for twitter. Maybe adjust the file-size when using it on the web. ==Currently at 1.5mb==. I would use .mp4 and embed that but apple iOS devices ==can't autoplay== or loop .mp4. Its a policy in safari. There are some obscure workaround to get .mp4 working in IOS safari but the workarounds are not trivial to implement.

### ~~A possible solution~~
1. ~~strip each frame from the .mov~~
2. ~~then open in photoshop~~
3. ~~then create .gif image~~
4. ~~then save, then add to "crown" .gif animation file.~~
5. ~~repeat over all frames~~
6. ~~get the meta data about frame rate from the .mov~~
7. ~~prompt the user about which frame-rate to use, and what file-name to user~~
8. ~~package the meta data to the "crown" .gif animation file~~

**NOTE:** you could ask some designers on twitter what they use. Then refer to this paragraph about what you are thinking about etc. Get some input etc. 

**NOTE:** Drop gif from github is good for quick and medium quality gif anims. The dropshows and gradients are not the best but good enough for examples or places were you have low bandwidth. 

**NOTE:** Twitter gif video size: Retina: 1100 × 764  (550 x 382)

### Tasks:  
- [x] Ask people that makes great gif anims on twitter and per email, that person that works for facebook, founders of gifrocket etc.
- [x] DO research into Adobe photoshop gif capabilities
- [ ] Do research into the other apps on the app store, email the creators of them.
- [ ] Can Automater.app extract frames from a mov?
- [ ] How do you extract frames from a .mov
- [ ] Can Automater.app make gif animations from jpgs
- [ ] Any online gif animation suits that works better if you have the frames?
- [ ] Do more online gif maker research. Find and list the available choices. 
- [x] ==Search google for blogs like: how to make gif videos etc==

<!--Foot-notes--> 

[^1]: Be aware, twitter has a max 5MB policy on gif animations. Twitter then converts this into an .mp4 file. So you always want to create your .gif videos as close to the 5mb limit as possible in order to get the best quality. 
[^2]: Photoshop may fail to save your .gif file, if the file is really big etc. What worked for me was to then reboot the computer and try again. Most likely its memory thing. I have a 15" Macbook Retina w/ Quad 2.3GHZ CPU, 8GB memory. So it should work if you have circa the same specs. You could try to adjust the settings or shrink the timeframe or size, this also work in some cases. ==Update==: photoshop may crash if the file is big, copy the error text. And google the solution. You have to set some permission and delete a file or 2. And then restart. 