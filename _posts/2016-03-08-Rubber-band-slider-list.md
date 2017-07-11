Case study of the RBSliderList<!--more-->. The RB is short for RubberBand. And its made by utilizing a a custom built ==animation engine== that delivers ==60FPS== butter smooth motion. It supports ==Rich interaction== combined with running tween animation. Apple doesn't provide this combo so it had to be made. The RBSliderList is the first component to use this animation engine. 

<img width="516" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/list_med_30fps.gif">

- 1. [Example](#example) 
- 2. [Notes](#notes) 
- 3. [Tasks](#tasks) 

## Example:

**swift:**  
  
```swift
StyleManager.addStylesByURL("~/Desktop/css/list.css")/*load the related style-sheets*/
StyleManager.addStylesByURL("~/Desktop/css/slider.css")
StyleManager.addStylesByURL("~/Desktop/css/sliderList.css")

let dp = DataProvider(FileParser.xml("~/Desktop/scrollist.xml"))/*load xml data to add to the DataProvider*/
let section = self.addSubView(Section(200, 200, self, "listSection")) as! Section/*adds some visual space around the component*/
sliderList = section.addSubView(RBSliderList(140,120,24,dp,section)) as? RBSliderList
```

## Animation engine:
Interactive spring dynamics. Friction, SpringStrength etc. The Spring solver has a small footprint and is easily customizable. 
An article describing how the animation engine works will come later. Stay tuned. 

**Supported easing classes:** 
- Linear
- EaseInQuad,EaseOutQuad,EaseInOutQuad
- EaseInCubic,EaseOutCubic,EaseInOutCubic
- EaseInQuart,EaseOutQuart,EaseInOutQuart
- EaseInQuint,EaseOutQuint,EaseInOutQuint
- EaseInSine,EaseOutSine,EaseInOutSine
- EaseInExpo, EaseOutExpo, ~~EaseInOutExpo~~

**Coming soon:**
- EaseInCirc, EaseOutCirc, EaseInOutCirc
- EaseInElastic,EaseOutElastic,EaseInOutElastic
- EaseInBack, EaseOutBack, EaseInOutBack
- EaseInBounce, EaseOutBounce, EaseInOutBounce

## Tasks:

- [ ] Add support for css animation
- [ ] Do research into singleton classes in swift
- [ ] Perfect the listItem css sizes. Its not Aligning correctly at the moment