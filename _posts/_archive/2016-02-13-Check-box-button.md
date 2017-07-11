Case study of the CheckBoxButton element<!--more-->


<img width="554" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/checkBoxButton_4.gif">
 
 
- 1. [Example](#example) 
- 2. [Notes](#notes) 
- 3. [Tasks](#tasks) 

## Example:


**swift:**  
  
```swift
let container = self.addSubView(Section(200, 200, self, "checkBoxButtonContainer")) as! Section/*this instance represents the inset shadow background and also holds the buttons*/
let checkBoxButton1 = container.addSubView(CheckBoxButton(120, 32,"Option 1",true,container)) as! CheckBoxButton
let checkBoxButton2 = container.addSubView(CheckBoxButton(120, 32,"Option 2",false,container)) as! CheckBoxButton

let checkGroup = CheckGroup(CheckParser.checkables(container),checkBoxButton1)/*Add the CheckBoxButtons to the checkGroup instance*/
func onEvent(event:Event){/*this is the event handler*/
    if(event.type == CheckGroupEvent.change){
        Swift.print("CustomView.onCheck() checked" + "\((event as! CheckGroupEvent).checked)")
    }
}
checkGroup.event = onEvent/*adds the event handler to the event exit point in the checkGroup*/
```


**CSS:**  
  
```css
#checkBoxButtonContainer{
	fill:#dddddd;
	float:left;
	clear:left;
	width:160px;
	height:24px;
	padding-left:12px;
	corner-radius:4px;
	drop-shadow:<InsetShadow>;
}
/*CheckBoxButton*/
CheckBoxButton{
	float:left;
	width:80px;
	height:14px;
	margin-top:5px;
}
CheckBoxButton Text{
	clear:none;
	width:100%;
	font:<DefaultFont>;
	size:12px;
	color:black;
	autoSize:left;
	margin-top:-2px;
}
CheckBoxButton CheckBox{
	margin-right:2px;
}
```

**NOTE:** You also need to import the CheckBox.css file and Text.css. The entire default style css system will be introduced in due time. Stay tuned.


## Notes:
- If you have many items to add to a CheckGroup you may use the CheckParser.checkables(view:NSView) to gather all elements that extends the ICheckable protocol

- The new event system is also implemented this time. No more delegation, no more key value observation, no more post notification calls, ==say hello to functional events, just 2 variables and 1 method==. Read more about it [here](http://stylekit.org/blog/2016/02/10/The-event-system/) 

## Tasks:
- [ ] The RadioButton element should highlight the text one selection unlike this element that shouldnt. Verify that this is the case. 