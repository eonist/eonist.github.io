This article will describe how the CSS system works. <!--more--> 

Content of page:
1.  [Line](#line)
2.  [Linear-Gradient](#linear-Gradient)
3.  [Corner-radius](#corner-radius) 
4.  [Float and clear](#float and clear) 
5.  [Import](#import) 
5.  [Margin](#margin)
6.  [Metrics](#metrics) 
7.  [Offset](#offset) 
8.  [States](#states) 
9.  [Syntax-tricks](#syntax-tricks) 
10. [TextField and TextFormat](#textField-and-textFormat) 
11. [Themes](#themes) 
12. [Undocumented private api](#undocumented-private-api) 
13. [Notes](#notes)

## Line

```
line shorthand in css
line: 4px solid #FF0000;/*Shorthand for element css border?*/

line:(thickness:4px, type:solid, color:#FF0000,,offset-type:inside); or border:(4px,solid,black,inside)
line-alpha:1;
line-offset-type:outside;
line-thickness:1;
line:linear-gradient(top,#C2C0C3,#C2C0C3);


line-offset-type:
line-offset-type:outside inside outside inside/*left right top bottom*/
line-offset-type-left:outside;
line-offset-type-right:outside;
line-offset-type-top:outside;
line-offset-type-bottom:outside;
```

## Linear-Gradient

```
Window{
	fill:linear-gradient(top,red,blue);
	fill:linear-gradient(top,gray 1 0,white 1 1);/*2 color gradient*/
	fill:linear-gradient(top,#638CF5,#1057E9);
	fill:linear-gradient(top,blue 1 0,green 1 1,orange 1 1);/*3 color gradient*/
}
```

## Corner-radius
```
corner-radius:4px;
corner-radius:10 20 10 20;(tl,tr,bl,br)(double and triple values is also possible)
corner-radius-top-left
corner-radius-top-right
corner-radius-bottom-left
corner-radius-bottom-right
```
Note: to support elliptical corners have a look at this code: `ElipticalCornerRectangle.macjournal`

## Float and clear
```
float:left/right/none
clear:left/right/none
```

Note: if you set both float and clear to none, you can position the element via code, ie in a sliderbutton

## Import
  
```css
@import url("styles/debug.css");
@import url("slider.css");
```

the path is relative to the main css (../ to get 1 level back)


## Margin

Margin:20px;
Margin:25%;(percentage of the parent width and height

Same for padding
line-Offset-type:outset,inside,center;//(outset,outside,,outside,inside); see illustrator name
Position:10px,10%,10;//same as above

Margin-left:20px;
margin-top:2px;

The order in which you specify the four values is important. It must be top, right, bottom, and left. If you get it wrong, you’ll be in trouble. In fact, the easiest way to keep the order straight is to remember to stay out of TRouBLe—top, right, bottom, and left.

When you’re using the same value for both top and bottom and another value for both left and right, you can use two values. margin: 0 2em; sets the top and bottom margins to 0 and the left and right margins to 2 ems. Likewise, if the top and bottom margins (or padding) differ, but the left and right remain the same, you can use three values. For example, margin: 0 2em 1em; sets the top margin to 0, the left and right margins to 2 ems, and the bottom margin to 1 em.

EMS:
Default ems size is 16px
12px = 0.75ems
24px= 1.5ems
36px = 2.25ems
48px = 3ems
60px = 3.75ems

## Metrics
support for percentages as a way of measure

so 
width:100%
height:50%

or 
offset:20%;
offset:50% 0%;
offset:20px 0px;


the percentage is calculated based on the parents width and height


width,height:100%;// means fill the parent space
size:auto(10px,10px),auto

also has support for ems

## Offset

```
offset:0 10; 
offset:12; /*same as offset:12,0*/
```

## States
   
```css
Button{
    color:grey;
}
Button:checked{
    color:blue;
}
Button:disabled:checked{
    color:black;
}
```


## Syntax-tricks:

```
Button{
    fill,font-color,line:blue; /*multiple properties with the same value)*/
}

Note: The later value and the later style always take property
```

## TextField and TextFormat


1em is the height of the web browser’s base text size, which is usually 16 pixels.

inherit. This keyword lets you force a style to inherit a value from a parent element

## Themes

primary secondary tertiary quaternary quinary senary septenary octonary nonary denary (1st,2nd,3,4th,5th,6th,7th,8th,9th,10th)
accent1 accent2

in real html and css setup you can then override these properties on each sub site, like a different theme for contact, about, portfolio etc
or you can simply load a summer theme in the summer and just ovveride the theme.css file
The same idea goes in the Element css setup, you use these values as classes and 

**Accent color:** 
Definition: Accent colors are colors that are used for emphasis in a color scheme. 
These colors can be bold or vivid and are used sparingly, to emphasize, contrast or create rhythm.





## Undocumented private api:

**Mask**
This could be a possibility (you may have legacy code for this)
```
mask:type(../assets/blob.svg);
mask:offset(20,20);
```
What if mask works like this:
Mask:1,none,3; //you set the array ref to the graphic you want to mask, none is don't mask


**blendmode**
```
blend:burn; // overlay colorburn etc
```


**Transform**

skew
scale
rotate
transform
translate


## URL

**Implement this:**

The URL itself is just like the HTML href attribute used for links, meaning you can use an absolute URL like http://www.missingmanuals.com/images/tile.gif, a root-relative path like /images/tile.gif, or a document- relative URL like ../../images/tile.gif. See page 244 for the full story on these kinds of paths.



## Notes: 
If a class is likely to get overridden then you should set the getClass() method to return the class type rather then relying on the self.dynamicType that the core Element class provides. Then you can return specific types in the subClass like: SpecialButton etc. 

Related posts:
- [Resolving CSS links](http://stylekit.org/blog/2015/11/04/Resolving-CSS-links/)   
- [The css box model](http://stylekit.org/blog/2016/01/04/The-css-box-model/) 
- The-style-linkage-system (article coming soon)

```swift
//Section relies on the default implementation of Element
//Button and SelectButton as well, as they are regularly overridden 
//Window can use the Class as the return value in getClass as Window usually can be targeted via the "id"
```

## Drop-shadow
```css
drop-shadow:drop-shadow(0px 0 #000000 0.8 1.6 1.6 2 2 false);
```
distance,angle-In-degrees,color,alpha,blur-x,blur-y,strength,quality,inner-shadow

**NOTE:** the strength and quality is inactive