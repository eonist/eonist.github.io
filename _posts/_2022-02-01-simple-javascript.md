My notes on some simple javascript for landing websites<!--more-->


### Examples:

```html
<!-- simple alert example -->
<html>
	<head>
	<script type = "text/javascript">
		function myfunction() {
			alert("how are you");
		}
	</script>
	</head>
	<body>
		<p>Click the following button to see the function in action</p>
		<input type = "button" onclick = "myfunction()" value = "Display">
	</body>
</html>
```

```html
<!-- changing element -->
<script type = "text/javascript">
	function replaceText(textVar) {
		document.getElementById('special_text').innerText = textVar // "test"
	}
</script>
<input type = "button" onclick = "replaceText('testing')" value = "Display">
```

```html
<!-- call js from a link:  -->
<a href="javascript:replaceText('testing')">Button</a>
```

```html
<!-- Importing -->
<head>  
	<script type = "text/javascript" src="function.js"></script>  
</head>  
```

```js
// looping
const arr = [3, 5, 7];
arr.foo = 'hello';
for (let i in arr) {
   console.log(i); // logs "0", "1", "2", "foo"
}
for (let i of arr) {
   console.log(i); // logs 3, 5, 7
}
for (let i = 0; i < targets.length; i++) {
  const target = targets[i];
}
```

```js
// event listener
element.addEventListener("click", myFunction);

function myFunction() {
  alert ("Hello World!");
}
```

```js
// get parent of element
console.log("this.parentElement: " + this.parentElement);
```

```js
window.onload = function () {
	// we have to wait for window to load before attaching eventlistners etc
}
```

```js
// prototype 
Element.prototype.hide = function() {
    this.style.display = 'none';
}
Element.prototype.show = function() {
    this.style.display = '';
}
// After this you can use the methods with the usual element identifiers like in these examples:

document.getElementByTagName('div')[3].hide();
document.getElementById('thing').show();
// or:

<img src="removeME.png" onclick="this.hide()">
```


### Resources:
https://www.javatpoint.com/how-to-call-javascript-function-in-html
