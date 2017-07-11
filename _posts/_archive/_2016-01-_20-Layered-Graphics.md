StyleKit supports layered graphics. The GraphicSkin can have many layers set at many depths. <!--more-->This is great when you want to style something with CSS3 but you don't want to make many graphic items for your layered design. 

>In essence what this means is that you can export anything you can design in Illustrator or Sketch and export it to pure CSS3 code and apply this CSS3 code to a Button or another UI element.

### Drawlab.io
Now illustrator and sketch doesn't have support for exporting to an obscure dialect of CSS3. In fact Sketch is pretty looked down them selfs. Ever tried to convert a sketch file on a windows machine? So I could write plugins for these apps. ==But I rather support an Open file format==. DrawLab relies on the same principle that Sketch uses. Namely an SQLite database file that has a table with two columns, name and blob, the blob column holds the vector data etc. Why use SQLite? because its fast. When an XML doc like .svg gets big, it gets cpu intensive to parse through it. SQLite on the other hand is lightning fast and can hold vast amounts of data without impacting the cpu. This sqlite format will be opensource and free for all to convert anyway they like. [My SQLite research](https://github.com/eonist/SqliteEdit) 

### Live-view  
The second reason is to support live view. Think Mac -> iPad. You adjust settings in DrawLab and instantly see the changes on your Mac app, iPad app, iPhone app on the devices them selfs. This could also be a plugin for illustrator or sketch but there might be problems. Stricter API rules down the line etc. Also creating an app when you have StyleKit isn't that hard. The third reason for the existence of DrawLab is its already complete. Only need to apply StyleKit to it. [drawlab.io](drawlab.io) "proof of concept" should be out early 2016.02


**Layered graphics example:**   

```css
Element{
	fill:blue,red;
	width:64px,32px;
	height:64px,32px;
}
```

<img width="257" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-19 at 21.09.20.png">

**Another example:** (CSS color as layer 1, SVGAsset as layer 2)  

```swift
StyleManager.addStyle("Element{fill:red,~/Desktop/icons/search.svg blue;}")
addSubView(Element(200,200))
```

<img width="309" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-20 at 17.30.33.png">

### OSX TitleBar Buttons
<img width="370" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/titlebar_buttons_anim.gif">  
[Retina version on Vimeo](https://vimeo.com/152491179)

The SVG graphic engine is working. Apparently the SVG graphics also animates quite nicely. You can load SVG from external sources like your desktop (nice for quick tests) or from internal asset folders in a project. Just prefix your URL with the tilde character "~" and you have relative paths. More animation examples comming soon.

**The css:**  

```css
Button{
	float:left;
	width:12px;
	height:12px;
	margin-left:8px;
	margin-top:4px;
}
Button#close{
	fill:assets/svg/icons/titlebar/close.svg none;
}
Button#minimize{
	fill:assets/svg/icons/titlebar/minimize.svg none;
}
Button#maximize{
	fill:assets/svg/icons/titlebar/maximize.svg none;
}
Button:over{
	fill:assets/svg/icons/titlebar/close_hover.svg none;
}
```
**And the swift code:**   

```swift
StyleManager.addStyle(css)
addSubview(Button(0,0,nil,"close"))
addSubview(Button(0,0,nil,"minimize"))
addSubview(Button(0,0,nil,"maximize"))

```

Great source for inspiration when adding support for easing animations through css: http://blog.invisionapp.com/crafting-easing-curves/
### Tasks:  

- [x] Add depth support in StyleKit
- [x] Implement support for setting width and height in CSS
- [x] Assert if the styling of SVG through CSS works
- [x] assert if SVGAsset can have none as fill, it didn't but its now been implemented
- [x] ==Add margin support for SVGAsset and AssetDecorator==
- [x] Fix the depth problem when a resolving style. 
- [ ] Test if style linking works
- [ ] Explore how you should set the hit-test-area. See old notes as well.
- [ ] add support for NaN as valid values for the Element.init() width and height arguments. 
- [ ] ==Make the IconBar Component==
- [ ] Make a window with custom SVG buttons and custom white background (el Capitan OSX close max min button)
- [ ] Implement support for setting offset in CSS
- [ ] Test the padding capabilities in CSS
- [ ] Implement syntax such as: `width,height:64px,32px;` (this should work, do a simple debug test)
- [ ] Add the float algorithm to StyleKit
- [ ] Apply StyleKit to your DrawLab.io proof of concept
- [ ] Make the pre Yosemite titlebar: derive css and code from here: TitleBarTest (for completeness, and its a greate example)
- [ ] Make an assert that checks if assetURL is prefixed with a tilde char. If true, use tilde expand with relative asset loading
- [ ] Try to make better gif animations with the usage of applescript and photoshop. 


