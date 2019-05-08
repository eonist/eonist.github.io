My notes on scripting with sketch<!--more-->.

### Steps:

Plugins > Custom Plugin option (Control+Shift+K). In the Custom Plugin sheet give your plugin a name and hit Save. Sketch will generate all the necessary files and folder structure for you.

### Example:

```javascript
var onRun = function(context) {
  //reference the Application
  var app = [NSApplication sharedApplication];
  //reference the Sketch Document
  var doc = context.document;
  //reference all the pages in the document in an array
  var pages = [doc pages];
  //loop through the pages of the document
  for (var i = 0; i < pages.count(); i++){
    //reference each page
    var page = pages[i];
    //get the name of the page
    var pageName = [page name];
    //show the page name in the console
    log(pageName);
  }
  //show a message at the bottom of Sketch
  doc.showMessage("MyPlugin Finished!");
  //send an alert message to the application
  [app displayDialog:"This document has "+ pages.count() + "pages." withTitle:"Alert Box Title"];
}
```

### Resources

- Simple example: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-1-28a5e3f01c68](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-1-28a5e3f01c68)

- import other scripts: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-3-writing-reusable-code-b0200dba7d6c](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-3-writing-reusable-code-b0200dba7d6c)

- more advance javascripting for sketch: [https://benfrain.com/creating-a-sketch-plugin-with-javascript/](https://benfrain.com/creating-a-sketch-plugin-with-javascript/)

- Has lots of sketch.api calls: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-5-the-mslayer-class-1868c5b6e1e8](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-5-the-mslayer-class-1868c5b6e1e8)

- Applying color style to an entire doc hierarchy and has import/export code: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-7-creating-a-shared-color-palette-9c0a1899ee02](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-7-creating-a-shared-color-palette-9c0a1899ee02)

- Center and scaling a shape: [https://github.com/turbobabr/Sketch-Plugins-Cookbook#8-scaling-layers](https://github.com/turbobabr/Sketch-Plugins-Cookbook#8-scaling-layers)

- Some file manipulation methods: [https://gist.github.com/abynim/04fd575a7e63ae2908a9](https://gist.github.com/abynim/04fd575a7e63ae2908a9)
