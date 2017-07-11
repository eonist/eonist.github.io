My Research-notes on core animation<!--more-->

<img width="532" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/CVDisplayLink_60fps_demo.mov.gif">  
Through sheer persistence/stubbornness I got CVDisplayLink working in swift with CALayer and CGContext (The holy grail of OSX/iOS animation) [also added this to vimeo](https://vimeo.com/156418351) 60 frames per sec. Silky smooth animation. Next up is trying it with some interactions as well. Also need to test it with Gradient etc. And path shifting. 

<img width="476" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/CATransitionDemo_full_24323.gif">  
CATransition doesn't really animate the properties, rather it crossfades 2 graphic states. ~~To animate properties take a look at this link:~~ [Listening for progress callbacks] and [vimeo link](https://vimeo.com/156419251) 

<img width="452" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/color_anim.mov.gif">  
The example above shows the humble beginnings of color tweening CA Layer CGContext (CATransition)  
 
**Content of page** 
 
- 1. [Example](#example) 
- 2. [Notes](#notes) 
- 3. [Brainstorming log](#brainstorming-log) 
- 4. [Tasks](#tasks) 
- 5. [Books](#books) 
- 6. [Resources](#resources) 
- 7. [Animation inspiration](#animation-inspiration) 
- 8. [Peripheral links](#peripheral-links) 

## Example:

Syntax Idea for implementing css animation with StyleKit:
**CSS:**  
  
```css
Button:over {/*<--animate when the button is in this state*/
  animation-name: Property;
  animation-duration: 3s;/*the duration of the anim*/
  animation-name: slidein;/*<--ref your anim link here*/
}

@keyframes slidein {/*<--this is the link id to the anim*/
  from {/*<--from this state*/
    margin-left: 100%;
    width: 300%; 
  }
  to {/*<--to this state*/
    margin-left: 0%;
    width: 100%;
  }
}
```
**Note:** the anim name can be used for the listeners in swift end and began listeners etc
**Note:** you may also add easing and other properties. Do more research into the correct props etc

**swift:**  
  
```swift
/*Nothing yet*/
```

## Notes:

- CADisplayLink: A CADisplayLink object is a timer object that allows your application to synchronize its drawing to the refresh rate of the display. [CADisplayLink] highly useful when the animation you want to create in an iOS app is something not easily handled by transitions or stock animations. CADisplayLink works very much like an NSTimer.  You can set it to call a method on every tick, but instead of setting a time interval, the time interval is determined by the screen’s refresh rate. apple also has some info: [apple CADisplayLink] You can also use timeStamp and duration to skip frames if the framerate drops etc. Or set the frameRate it self to 30fps. You cant go above 60fps it seems. Aka the native device fps. ==CADisplayLink gives us a much better resolution that a NSTimer== to measure percentage complete use: `percentComplete = elapsedTime / animationDuration;`

- CAKeyframeAnimation

- CAMediaTiming 

- **actionForLayer** ~~it seems that this is the method you override if you want to animate custom properties~~ [This has an example](https://www.objc.io/issues/12-animations/animating-custom-layer-properties/)  

- CABasicAnimation

- What not todo: http://www.raywenderlich.com/102590/how-to-create-a-complex-loading-animation-in-swift  1000++ lines of code to make a tiny animation. surly this should be handled by an animation suite that defines your anim in an xml like structure and then uses a Util method to play the sequence. You even have code on how to implement this.


turn of implicit anim http://stackoverflow.com/questions/2244147/disabling-implicit-animations-in-calayer-setneedsdisplayinrect?rq=1  and http://stackoverflow.com/questions/30986736/how-to-apply-complex-calayer-transform-changes

## Tasks:
- [x] Is there any onAnimEnded and onAnimBegan call-backs? A: animationDidStart, animationDidStop
- [ ] Keyframe anim for your SVG keyframe anim idea
- [x] Animating color. 
- [ ] ==Add a Selector hock into the mover class so that you can get the movementStopped call==
- [ ] try to animate a path transformation from rect to round rect. and find that path anim link
- [ ] try animating from a gradient to another. Use the green gradient to orange. 
- [ ] Look in the awesome swift repos for elastic rubberBand animation and frame-animation etc
- [ ] Research ~~CADisplayLink~~ CVDisplayLink for OSX <- looks important (CADisplayLink is for IOS)
- [ ] look through your books
- [ ] look into gamedev for inspiration. 
- [ ] Find IOS and OSX game books
- [ ] ==Try to implement the [CADisplayLink] with the [log10 anim] for easing== 
- [ ] Find the info on how to morph between bezier shapes (event with different anchor points)
- [x] do this tutorial: [super simple CADisplayLink tutorial]
- [x] Watch the Session 421 - Core Animation Essentials video from WWDC 2011 this was a waste of time. shallow anim concepts only
- [ ] read this on [removeOnComplete bug]
- [ ] look into that cocoapod UI example website. Find the link first maybe https://twitter.com/cocoacontrols
- [x] I think you need to watch the CALAyer WWDC videos again. To get the animation working.
- [ ] see if you can resize frame. 
- [x] see if you can make a fps ticker with CVDisplayLink
- [ ] check what other frameworks are using facebook pop <-- apparently they are doing it wrong, twui, ~~framerjs~~<-not swift or objc, ~~cosmicminds UI ~~ <-- they use stock animation    

## Books:

Foundation Animation: Making Things Move!  http://www.amazon.com/Foundation-Actionscript-3-0-Animation-Making/dp/1590597915/ref=sr_1_3?ie=UTF8&qid=1456074234&sr=8-3&keywords=foundation+actionscript

Foundation Game Design ==(has Bouncing and gravity chapters)== http://www.amazon.com/Foundation-Game-Design-ActionScript-3-0/dp/143023993X/ref=pd_sim_14_6?ie=UTF8&dpID=51T7pjLcQ5L&dpSrc=sims&preST=_AC_UL160_SR130%2C160_&refRID=1K0K4F63TKD756Z8QGBA

## Resources:  
- Basic core anim: https://www.objc.io/issues/12-animations/animations-explained/  
- Some nice core anim features: http://oleb.net/blog/2010/12/animating-drawing-of-cgpath-with-cashapelayer/  
- Gradient animation (This is for the CAGradientLayer so not really usefull): http://tumbljack.com/post/188089679/gpu-accelerated-awesomeness-with-cagradientlayer
- identifying onComplete when using many animations: http://stackoverflow.com/questions/1255086/how-to-identify-caanimation-within-the-animationdidstop-delegate
- [Listening for progress callbacks]
- HitTesting animated layers: http://ronnqvi.st/hit-testing-animating-layers/ also a pdf copy here: https://dl.dropboxusercontent.com/u/2559476/Hit%20testing%20animating%20layers.pdf
- log10 rubber band anim example: [log10 anim]
- [Another CADisplayLink tutorial]
- Watch the Session 421 - Core Animation Essentials video from WWDC 2011. It's only an hour and it is ~~extremely informative.~~  https://developer.apple.com/videos/play/wwdc2011-421/
- [super simple CADisplayLink tutorial]


## Animation inspiration:
- wobbly interface: https://github.com/brocoo/BRFlabbyTable
- nice pull to refresh tutorial: http://ronnqvi.st/controlling-animation-timing/
- facebook image paning tutorial from the app paper: http://subjc.com/facebook-paper-photo-panner/
- Text drawing animation from real text: http://oleb.net/blog/2010/12/animating-drawing-of-cgpath-with-cashapelayer/
- interesting playback scrubber for ios: http://subjc.com/castro-playback-scrubber/
- parallax animation tutorial: http://mysterycoconut.com/blog/2011/09/cag4/
- UIKit dynamics: https://www.objc.io/issues/12-animations/interactive-animations/

## Peripheral links:
- More nuanced info on CADisplayLink: http://www.ananseproductions.com/game-loops-on-ios/
- This framework for IOS uses CADisplayLink and also has easing: https://github.com/intuit/AnimationEngine
- video player with cadisplaylink https://developer.apple.com/library/ios/samplecode/AVBasicVideoOutput/Introduction/Intro.html#//apple_ref/doc/uid/DTS40013109
- performance testing cadisplaylink on mobile devices (Layer Trees vs. Flat Drawing ): http://floriankugler.com/2013/05/24/layer-trees-vs-flat-drawing-graphics-performance-across-ios-device-generations/
- Discussion on swift and CVDisplayLink open gl and using NSTimer as a substitute: http://stackoverflow.com/questions/25981553/cvdisplaylink-with-swift
- GrandCentralDispatch based timer, for async timing: (can be used for frame anim) http://www.fieryrobot.com/blog/2010/07/10/a-watchdog-timer-in-gcd/


## CVDisplayLink resources: 
- This code works in swift: https://github.com/steven741/basic-opengl-swift/blob/45179a646569fcb66738a2c68358df0d5ff458f8/Demo/OpenGLView.swift
-  indepth discussion about CVDisplayLink and CALAyer: http://stackoverflow.com/questions/7610117/layer-backed-openglview-redraws-only-if-window-is-resized  


## CPU Threading  
- I depth analysis of the state of UI CPU Threading: http://chat.stackoverflow.com/rooms/104337/opengl-on-macos-x-and-threading pdf copy [here](https://dl.dropboxusercontent.com/u/2559476/open GL animation and swift – Conversation in OpenGL on MacOS X and Threading.pdf) 
  
<!--Links, this section wont be visible to the readers-->
[Listening for progress callbacks]:http://stackoverflow.com/questions/18827973/core-animation-progress-callback?lq=1
[CADisplayLink]:https://zearfoss.wordpress.com/2011/09/02/more-cadisplaylink/
[log10 anim]:https://medium.com/thoughts-on-thoughts/recreating-apple-s-rubber-band-effect-in-swift-dbf981b40f35#.tpbvwo1wi
[apple CADisplayLink]:(https://developer.apple.com/library/ios/documentation/QuartzCore/Reference/CADisplayLink_ClassRef/index.html)
[Another CADisplayLink tutorial]:http://holko.pl/2014/06/26/recreating-skypes-action-sheet-animation/
[super simple CADisplayLink tutorial]:http://stackoverflow.com/questions/34054861/uiimageview-rotation-animation-using-cadisplaylink-tracking-rotation?rq=1
[removeOnComplete bug]:https://gist.github.com/d-ronnqvist/11266321
[Calculating the physics]:https://www.objc.io/issues/12-animations/interactive-animations/
[UI CPU Threading]:https://dl.dropboxusercontent.com/u/2559476/OpenGL%20on%20MacOS%20X%20and%20Threading%20%7C%20chat.stackoverflow.com.pdf

