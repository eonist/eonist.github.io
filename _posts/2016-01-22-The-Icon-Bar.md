A case study of the icon bar<!--more-->: 

<img width="474" alt="task-bar-animation" src="https://raw.githubusercontent.com/stylekit/img/master/the_icon_bar_anim_x2p.gif">

### Swift:
This example uses a ==Section element== to contain the SelectButtons:  
  
```swift
let section = addSubView(Section(0, 0, "Icon button: ", self, "iconButtonSection")) as! Section;
let btn1 = section.addSubView(SelectButton(0,0,false,section,"first")) as! SelectButton;
let btn2 = section.addSubView(SelectButton(0,0,false,section,"second")) as! SelectButton;
let btn3 = section.addSubView(SelectButton(0,0,false,section,"third")) as! SelectButton;
let btn4 = section.addSubView(SelectButton(0,0,false,section,"fourth")) as! SelectButton;
section.addChild(SelectParser.selectables(section),btn2)
```
### CSS:
This example uses CSS linkage. Basically there are a few generic css styles that are reused. These are stored in the generic.css file which are ==imported through the css==. This type of system is the same you would find in any other css system. A case study of the linkage system will be introduced at a later date.  
  
```css
@import url("osx/generic.css");
#iconButtonSection SelectButton{
	float:left;
	clear:none;
	width,height:24px,24px;
	line:grey7,none;
	line-offset-type:outside;
	line-alpha:1;
	line-thickness:1px;
	drop-shadow:<BlankShadow>,none;
	margin-top:0px,1px;
	margin-left:0px,1px;
}
#iconButtonSection SelectButton#first{
	clear:left;
	fill:<ButtonBase>,assets/svg/icons/view/icons.svg grey5;
	corner-radius:4px 0px 4px 0px;
}
#iconButtonSection SelectButton#first:selected{
	fill:<ButtonBase:selected>,assets/svg/icons/view/icons.svg white;
}
#iconButtonSection SelectButton#second{
	fill:<ButtonBase>,assets/svg/icons/view/list.svg grey5;
	corner-radius:0px;
	line-offset-type-right,line-offset-type-left:inside;
}
#iconButtonSection SelectButton#second:selected{
	fill:<ButtonBase:selected>,assets/svg/icons/view/list.svg white;
}
#iconButtonSection SelectButton#third{
	fill:<ButtonBase>,assets/svg/icons/view/columns.svg grey5;
	corner-radius:0px;
	line-offset-type-right:inside;
}
#iconButtonSection SelectButton#third:selected{
	fill:<ButtonBase:selected>,assets/svg/icons/view/columns.svg white;
}
#iconButtonSection SelectButton#fourth{
	fill:<ButtonBase>,assets/svg/icons/view/coverflow.svg grey5;
	corner-radius:0px 4px 0px 4px;
}
#iconButtonSection SelectButton#fourth:selected{
	fill:<ButtonBase:selected>,assets/svg/icons/view/coverflow.svg white;
}
#iconButtonSection SelectButton:selected{
	drop-shadow:<InsetShadow>,none;
}
```

If you want to add eventlisteners to the SelectGroup:
  
```swift
NSNotificationCenter.defaultCenter().addObserver(self, selector: "onSelect:", name: SelectGroupEvent.select, object: selectGroup)
func onSelect(notification: NSNotification) {
    Swift.print("TestSelectGroup.onSelect()")
    Swift.print("TestSelectGroup.onSelect: " + String(notification.object))/* as ISelectable).isSelected*/
}
```

Adding eventlisteners to Button:
  

```swift
NSNotificationCenter.defaultCenter().addObserver(self, selector: "onButtonDown:", name: ButtonEvent.down, object: closeButton)
func onButtonDown(sender: AnyObject) {
    Swift.print("WinView.onButtonDown() ")
    //let textButton:Button = (sender as! NSNotification).object as! Button
    /*
    if((sender as! NSNotification).object === self.textButton!){
    Swift.print("sender.object === self.textButton")
    }
    */
    
    Swift.print("object: " + String((sender as! NSNotification).object))
    Swift.print("name: " + String((sender as! NSNotification).name))//buttonEventDown
    Swift.print("userInfo: " + String((sender as! NSNotification).userInfo))//nil
    //Swift.print("WinView.onButtonDown() Sender: " + String(sender))
}

/*These are the other events that Button dispatches:*/
ButtonEvent.releaseInside
ButtonEvent.releaseOutside
ButtonEvent.rollOut
ButtonEvent.rollOver
```

### Tasks:
- [ ] Fix the over:state bug
- [ ] Find examples of icon-bar designs
- [ ] Find examples of icon-bar animations
- [ ] Make another example with a different "look" 