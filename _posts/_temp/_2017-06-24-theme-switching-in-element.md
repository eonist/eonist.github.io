Theme switching with Element<!--more--> 

### Swift code:

```swift
window.contentView = InteractiveView2()
        
StyleManager.addStylesByURL("~/Desktop/theme/lighttheme.css")

let section = window.contentView!.addSubView(Section(200,300))
let btn = section.addSubView(Button(NaN,NaN,section,"btn"))
let one = section.addSubView(Element(100,100,section,"one"))

btn.event = { event in
    if event.type == ButtonEvent.upInside {
        Swift.print("value: " + "\(StyleManager.getStylePropVal("Theme", "fill"))")//white
        StyleManager.reset()//removes and resets all styles stored in StyleManager
        StyleManager.addStylesByURL("~/Desktop/theme/darktheme.css")//Add a new theme
        ElementModifier.refreshSkin(section)//Re-Applies the styles
        Swift.print("newVal: " + "\(StyleManager.getStylePropVal("Theme", "fill")))")//black
    }
}
```

### CSS code:

This is the Dark theme, it imports the general.css which has all the main css. 

```css
/*DarkTheme*/
@import url("general.css");
Theme{
	fill:black;
}
```

This is the Light theme, it imports the general.css which has all the main css.

```css
/*LightTheme*/
@import url("general.css");
Theme{
	fill:white;
}
```

This css has all the general css has all the main css.

```css
/*GeneralTheme*/
#btn{
	fill:blue;
	width:100px;
	height:24px;
	float:left;
	clear:left;
}
#one{
	fill:<Theme>;
	clear:left;
	float:left;
}
```