For me it's hard to use something exciting and trying to customize it. When something has matured, it's hard to wrangle it to be something else. Unless the changes are miniscule. They often are not. Building something from scratch is sort of simpler, because you can always start with simple bricks and a state that is pristine and has no legacy and things to worry about. But what do you start with?<!--more-->

Sketch out the design in a notebook, one for desktop, and one for mobile. And use figma or sketch to make the blueprints how the site should look. Use a 12 column grid with gutter and margins. Google this to get the sizes needed. Elements should snap to the 12 column grid. try to use simple row spacing. like 6, 12, 24, 32, 48, 96, 128 etc.

1. Start with the fundamentals. Simple divs. Simple colors. Figure out the basic layout alignments. Just create pseudo code at first to just figure out the scope. Then comment out everything but the basics, and slowly convert the pseudo code to production code. We do this because HTML and CSS is such a messy language, and you often times get stuck if you start coding production code from the beginning. Basics of css: `#id` is more unique elements and `.class` common reusable elements (CSS, HTML)
2. Add Simple text and dummy images (CSS, HTML)
3. Start adding links and sub-sites (CSS, HTML)
4. Start moving html code and css into a backend system, wordpress, jekyll etc
5. Once the site works for desktop. It's now time to retro-fit it for mobile.

Happy coding

### Some tips:

**Centering things vertically:** 😅

```
outer div:
display: flex;
inner div:
margin: auto;
```
Alt 1:
```
outer:
justify-content: center;
display: flex;
inner:
align-self: center;
```
Alt 2:
```
outer:
display: flex;
justify-content: center;
align-items: center;
```

# Centre stuff in css:
https://www.w3schools.com/css/css_align.asp

### Center Align Text
```css
.center {
  margin: auto;
  width: 50%;
  border: 3px solid green;
  padding: 10px;
}
```

### Center Align Text
```css
.center {
  text-align: center;
  border: 3px solid green;
}
```

### Center an Image
```css
img {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width: 40%;
}
```

### Center Vertically - Using padding
Drawback is that you have don't have control over final height of the element
```css
.center {
  padding: 70px 0;
  border: 3px solid green;
  text-align: center;
}
```

### Center Vertically - Using line-height

```css
.center {
  line-height: 200px;
  height: 200px;
  border: 3px solid green;
  text-align: center;
}

/* If the text has multiple lines, add the following: */
.center p {
  line-height: 1.5;
  display: inline-block;
  vertical-align: middle;
}
```

### Center Vertically - Using position & transform
```css
.center {
  height: 200px;
  position: relative;
  border: 3px solid green;
}

.center p {
  margin: 0;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

### Center Vertically - Using Flexbox
**This one is good**. also works for multiline text
```css
.center {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 200px;
}
```

### target nth:
```css
header nav ul li:nth-of-type(1) {
	background-color: cyan;
}
header nav ul li:nth-of-type(2) {
	background-color: yellow;
}
header nav ul li:nth-of-type(3) {
	background-color: magenta;
}
```
