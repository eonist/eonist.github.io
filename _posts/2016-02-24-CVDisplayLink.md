 CVDisplayLink is a timer object that allows your application to synchronize its drawing to the refresh rate of the display. ==It's highly useful when you want to combine animation with user interactions==, something that is not easily handled by transitions or stock animations.<!--more--> 
 
 <img width="258" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/CVDisplayLink_60fps_Anim_plus_interactivity_demo.mov.gif">  
 CVDisplayLink 60fps rich animation + interactivity demo: [vimeo probably at 30fps?](https://vimeo.com/156972617) and  [original video in 60fps @dropbox](https://dl.dropboxusercontent.com/u/2559476/CVDisplayLink_60fps_Anim_plus_interactivity_demo.mov) and the code for this [here](https://github.com/eonist/GitSyncOSX/blob/master/src/window/view/TestView3.swift)  and [here](https://github.com/eonist/GitSyncOSX/blob/master/src/window/view/experiment/VerticalThrowArea.swift) 
 
 - 1. [Example](#example) 
 - 2. [Q & A](#Q-&-A) 
 - 3. [Tasks](#tasks) 
 
## Example: 
Older frame animation technology at 24fps [vimeo](https://vimeo.com/156673422) 

## Description:
CVDisplayLink is the holy grail of rich animation & interaction in OSX and IOS. CVDisplayLink works very much like an NSTimer. You can set it to call a method on every tick, but instead of setting a time interval, the time interval is determined by the screen’s refresh rate. You can also use timeStamp and duration to skip frames if the frame-rate drops etc. Or set the frameRate it self to 30fps. You cant go above 60fps it seems. Aka the native device fps. ==CVDisplayLink gives us a much better time-resolution than NSTimer== to measure percentage complete use: `percentComplete = elapsedTime / animationDuration;` 

## Q & A
**Q:** Why use CVDisplayLink over NSTimer to do frame-animation:  
**A:** Because CVDisplayLink will make sure that rendering is in sync with your screen refresh rate and the graphical sub-system. It makes no sense to render more images than your monitor can physically display or your sub-system can actually process. Also a timer doesn't fire at precise intervals, ==if you miss a screen refresh only by 0.01 ms, then the whole image will be delayed by a full screen refresh== (causing animation lag and reducing the number of rendered images displayed a second). The CVDisplayLink is like a high priority optimized timer.

**Q:** CVDisplayLink must draw on the main thread to be visible. What happens then if there is some other computational heavy process on the main thread while heavy animation is going on? Wont the frame-rate go down and even start to be really slow? 
**A:** If you cannot achieve a good frame-rate that way, since your main thread is busy elsewhere, it is recommend to rather move everything else (pretty much your whole application logic) to other threads (e.g. using Grand Central Dispatch) and ==only keep user input and drawing code on the main thread==.  

**Q:** Will the human brain notice if a frame is dropped at 60fps? So 59fps for one second and then back to 60fps the next? Or does the dropped frame have other implications?

**A:** "Unfortunately the animation still stuttered away like an old tractor plowing through a barren potato field during dry season." source: http://www.bigspaceship.com/ios-animation-intervals/

## Facebook pop:
Pop is the framework that Facebook uses to power the animations in their app Paper. It’s also authored by Kimon Tsinteris, one of the creators of the Our Choice iPad app. Pop works differently from JNWSpringAnimation as it doesn’t use Core Animation, ==it relies on a timer firing every 1/60th of a second (CADisplayLink) to govern the motion of elements so it can recalculate their position easily if a gesture interrupts the animation.==

## CVDisplayLink (OSX)
- CVDisplayLink referenced from objc.io article: https://github.com/objcio/issue-12-interactive-animations-osx
- CVDisplayLink example that draws on the main thread (its unknown if it works or not): https://github.com/00buggy00/OpenGL-Swift-and-CVDisplayLink/blob/master/SwiftOpenGL/SwiftOpenGLView.swift
- potential solution to resizing the frame when using CVDisplayLink: http://jwilling.com/blog/osx-animations/
- lots of detailed workarounds to get CVDisplayLink working: stackoverflow [link](http://stackoverflow.com/questions/7610117/layer-backed-openglview-redraws-only-if-window-is-resized/11213382?noredirect=1#comment58864575_11213382) 
- swift + NSOpenGlView + CVDisplayLink example that seemingly works: http://stackoverflow.com/questions/25981553/cvdisplaylink-with-swift although i'm unable to get it working. 
- This is article is important: http://stackoverflow.com/questions/9442657/draw-from-a-separate-thread-with-nsopengllayer/11437833#11437833
- This convo is important: http://chat.stackoverflow.com/rooms/104337/opengl-on-macos-x-and-threading Thanks again Mecki for your guidance. 
- Swift 3 CVDisplayLink: [here](https://3d.bk.tudelft.nl/ken/en/2016/11/05/swift-3-and-opengl.html) 
##CADisplayLink (IOS)
- https://www.objc.io/issues/5-ios7/view-controller-transitions/ (some insights here)

## Core animation (IOS): 
- This article describes why anim + interaction doesnt work if you just use Core animation (great read): http://initwithfunk.com/blog/2014/05/22/interactive-animated-transitions-on-ios/

## General notes:  
- If you want to **chain animation** with the CVDisplayLink: Then never stop the CVDisplayLink before you start another. Since you cant start a CVDisplayLink within within the lifespan of a CVDisplayLinkStart "call". 

## Tasks
- [x] create an Example with CVDisplayLink and interactivity
- [x] ==Test CVDisplayLink with NSTextField and NSTextView==
- [ ] Test CVDisplayLink with more complex gradients and dropshadows and many more shapes. 
- [ ] Create a system where you don't draw views that are out of view. 
- [ ] Try to implement scroll gesture the same way you implemented "mouse-throw" See notes about this in your core anim article
- [ ] Try to implement the spring ball demo. 