Case study of the LeverSpinner element <!--more-->

<img width="550" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/leverSpinner_1_x14hs.gif">

- 1. [Example](#example) 
- 2. [Notes](#notes) 
- 3. [Tasks](#tasks) 

## Example

**Swift:**
  
```swift
let leverSpinner:LeverSpinner = addSubView(LeverSpinner(140, 40,"Value: ", 0, 1, CGFloat(Int.min), CGFloat(Int.max), 0, 100, 200, self)) as! LeverSpinner;
func onEvent(event:Event){
	if(event.origin === leverSpinner && event.type == SpinnerEvent.change){(event as! SpinnerEvent).value}//here we can read the value 
}
leverSpinner.event = onEvent//We add an event handler 
```

In the above example there is also an example of the new super simple event system, which also works inside methods 🚀

**CSS:**  
  
```css
Spinner{
	float:left;
	clear:left;
	width:120px;
	height:24px;
	padding:-2px;
}
Spinner TextInput{
	width:90px;
	height:18px;
	margin-right:6px;
}
Spinner TextInput Text{
	width:40px;
	height:20px;
	autoSize:left;
	color:black;
	selectable,mouseEnabled:false;
	margin-top:0px;
}
Spinner TextInput TextArea{
	width:50px;
	height:18px;
}
Spinner TextInput TextArea Text{
	width:100%;
	align:right;
	selectable,mouseEnabled:true;
}
Spinner Stepper{
	clear:none;
}
```

## Notes:
- The variables in the css named `<InsetShadow>` etc. Are generic theme styles stored in a css file. An article describing how to use generic styles is coming soon. 
- The css for the Spinner component only (you also need css for the stepper. This will be introduced shortly)

## Tasks:
- [ ] Fix window movement when using custom window, only top should be draggable
- [ ] Should not increment or decrement via upInside if movement has occurred
- [ ] Change the Event system from calling the event variable first to calling the event method first.