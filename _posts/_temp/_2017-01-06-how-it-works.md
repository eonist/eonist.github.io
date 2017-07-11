Element consists of 3 parts: <!--more--> 


<img width="440" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Element_parts.png">

# Skin:

There are 2 types of Skin types:

<img width="364" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/skin_types.png">

GraphicSkin:

<img width="312" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/graphic_types.png">

Each GraphicSkin has multiple layered graphics: 
```css
Button{
	fill:green,yellow,red;/*The 'comma' char works with layers*/
	fill-alpha:0.5;/*Applies to all layers*/
	corner-radius:0,0,0;/*Applies to individual layers*/
}
```
<img width="350" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/skin_layers.png">

Each Graphic has 2 parts:

<img width="298" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/graphics_parts.png">



# State:

The state is just a string that changes over time when the user interacts with the Element. Can be overDownCheckedFocused etc. then the Element is styled accordingly, by looking for a style that matches this combination of states.

<img width="264" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/state_types.png">

# Style.

A style has Selectors, StyleProperties 1. A StyleProperty contains all the graphics attributes that can style a Skin 2. A Selector contains the address of which Element should be styled