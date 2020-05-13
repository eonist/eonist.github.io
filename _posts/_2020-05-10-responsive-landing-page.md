My notes on how to make a responsive landing page<!--more-->The idea is to make each page make up the size of your macbook / iPhone screen like in the example bellow

<img width="300" alt="img" src="https://github.com/stylekit/img/blob/master/one-pager-sections.gif?raw=true">

### The html

```html
<!DOCTYPE html>
<link rel="stylesheet" type="text/css" href="stylesheets/section.css">
<html>
    <body>
		 <div class="outer" id="one">
			 <div class="inner">
				 test
			 </div>
		 </div>
		 <div class="outer" id="two">
			 test
		 </div>
		 <div class="outer" id="three">test</div>
		 <div class="outer" id="four">test</div>
    </body>
</html>
```

### The CSS:

```css
body {
  background-color: white;
}
html, body {
     height: 100%; /*This is the key to making each page fit your screen*/
     margin: 0px;
 }
 .outer {
	 height: 100%; /*This is the key to making each page fit your screen*/
 }
 #one  {
     background: purple;
 }
 #two  {
     background: green;
 }
 #three  {
     background: red;
 }
 #four  {
	  background: blue;
 }
```
