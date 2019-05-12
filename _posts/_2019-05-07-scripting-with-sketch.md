My notes on scripting with sketch<!--more-->.

### Steps:

Plugins > Custom Plugin option (ctrl + shift + K). In the Custom Plugin sheet give your plugin a name and hit Save. Sketch will generate all the necessary files and folder structure for you.

### Example:

```javascript
var onRun = function(context) {
  var app = [NSApplication sharedApplication];//reference the Application
  var doc = context.document;//reference the Sketch Document
  var pages = [doc pages]; //reference all the pages in the document in an array
  for (var i = 0; i < pages.count(); i++){//loop through the pages of the document
    var page = pages[i]; //reference each page
    var pageName = [page name];//get the name of the page
    log(pageName);//show the page name in the console
  }
  doc.showMessage("MyPlugin Finished!");//show a message at the bottom of Sketch
  [app displayDialog:"This document has "+ pages.count() + "pages." withTitle:"Alert Box Title"];//send an alert message to the application
}
```

## Basic example:
```javascript
log('This is an example Sketch script.');
var documentName = context.document.displayName();
log('The current document is named: ' + documentName);
var selectedLayers = context.selection;
var selectedCount = selectedLayers.count();
if (selectedCount == 0) {
  log('No layers are selected.');
} else {
  log('Selected layers:');
  for (var i = 0; i < selectedCount; i++) {
    var layer = selectedLayers[i];
    log((i+1) + '. ' + layer.name());
  }
};

```

### Resources

- Simple example: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-1-28a5e3f01c68](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-1-28a5e3f01c68)

- import other scripts: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-3-writing-reusable-code-b0200dba7d6c](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-3-writing-reusable-code-b0200dba7d6c)

- more advance javascripting for sketch: [https://benfrain.com/creating-a-sketch-plugin-with-javascript/](https://benfrain.com/creating-a-sketch-plugin-with-javascript/)

- Has lots of sketch.api calls: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-5-the-mslayer-class-1868c5b6e1e8](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-5-the-mslayer-class-1868c5b6e1e8)

- Applying color style to an entire doc hierarchy and has import/export code: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-7-creating-a-shared-color-palette-9c0a1899ee02](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-7-creating-a-shared-color-palette-9c0a1899ee02)

- Center and scaling a shape: [https://github.com/turbobabr/Sketch-Plugins-Cookbook#8-scaling-layers](https://github.com/turbobabr/Sketch-Plugins-Cookbook#8-scaling-layers)

- Some file manipulation methods: [https://gist.github.com/abynim/04fd575a7e63ae2908a9](https://gist.github.com/abynim/04fd575a7e63ae2908a9)

- writing json: [https://gist.github.com/aaronash/054cc8955420ebeaee90](https://gist.github.com/aaronash/054cc8955420ebeaee90)

- ⭐ Lots of scripts to all possible scenarios: ⭐ [https://github.com/bomberstudios/sketch-commands/tree/master/Sketch%20Commands.sketchplugin/Contents/Sketch](https://github.com/bomberstudios/sketch-commands/tree/master/Sketch%20Commands.sketchplugin/Contents/Sketch)

- overview of type constants: [https://github.com/turbobabr/sketch-constants/blob/master/src/index.js](https://github.com/turbobabr/sketch-constants/blob/master/src/index.js)

- Exporting layer data to xml and json 👌: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-6-exporting-data-36865e571122](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-6-exporting-data-36865e571122)
