Case study of the RadioBullet element <!--more-->

<img width="500" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/radiobullets_crop_low.gif">

- 1. [Example](#example) 
- 2. [Notes](#notes) 
- 3. [Tasks](#tasks) 

## Example

**Swift:**
  
```swift
let container = addSubview(Section(0,0,self,"radioBulletContainer")
let btn1 = container.addSubView(RadioBullet(0,0,true,container))
let btn2 = container.addSubView(RadioBullet(0,0,false,container))
addSubview(SelectGroup([btn1,btn2],btn1))
```

**CSS:**
  
```css
#radioBulletContainer{
	fill:#dddddd;
	float:left;
	clear:left;
	width:52px;
	height:19px;
	padding-left:12px;
	padding-top:5px;
	corner-radius:4px;
	drop-shadow:<InsetShadow>;
}
/*RadioBullet*/
RadioBullet{
	float:left;
	fill:<RadioBulletBase>,none,none,none,none;
	width,height:14px;
	corner-radius:7px;
	drop-shadow:<SubtleShadow>,none,none,none,none;
	margin-right:12px;
}
RadioBullet:selected{
	fill:<RadioBulletBase:selected>,<RadioBulletBottomShine>,<RadioBulletTopShine>,<RadioBulletBulletShine>,#021931;
	width,height:14px,14px,14px,14px,5px;
	corner-radius:7px,7px,7px,7px,2.5px;
	margin-left,margin-top:0px,0px,0px,0px,4.5px;
}
```

## Notes:
- The variables in the css named `<InsetShadow>` etc. Are generic theme styles stored in a css file. An article describing how to use generic styles is coming soon. 
- The CSS floating system is also included in this example. Read more about that [here](http://stylekit.org/blog/2016/01/27/Floating-algorithm/)

## Tasks:
- [ ] Implement support for hit-testing the skin layers
- [ ] Fully debug the floating system