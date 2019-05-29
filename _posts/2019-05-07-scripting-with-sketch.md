My notes on scripting with sketch<!--more-->.

### Steps:

Plugins > Custom Plugin option (ctrl + shift + K). In the Custom Plugin sheet give your plugin a name and hit Save. Sketch will generate all the necessary files and folder structure for you. The plugin will now reside in: `~/Library/Application\ Support/com.bohemiancoding.sketch3/Plugins`

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
### Tips:

- You can use the log() function to write to the System Console (MacOS) to see output. This is where you will also see exceptions. Just be sure to search for Sketch in the search bar to filter out other applications, and also your own log keyword, to get only plugin specific log outputs.

- Force sketch to reload script under development: Terminal: `defaults write ~/Library/Preferences/com.bohemiancoding.sketch3.plist AlwaysReloadScript -bool YES`


### Making the plugin:

1. Add Manifest.json and Main.js to a folder called Sketch
2. Add the sketch folder into a new folder named: MyPlugin.sketchplugin. Place this in here: `~/Library/Application\ Support/com.bohemiancoding.sketch3/Plugins/`
3. Fire up sketch and see your plugin in the plugin menu
4. Start splitting up your code into modules. And then import them by `@import 'common.js'` Then you are able to organise the code better.

### .sketchplugin structure:
```
MyPlugin.sketchPlugin
└── Contents
    ├── Resources
        └── icon.png
    └── Sketch
        ├── manifest.json
        └── MyClass.js
```

### Resources

- ⭐ Lots of scripts to all possible scenarios: ⭐ [https://github.com/bomberstudios/sketch-commands/tree/master/Sketch%20Commands.sketchplugin/Contents/Sketch](https://github.com/bomberstudios/sketch-commands/tree/master/Sketch%20Commands.sketchplugin/Contents/Sketch)

- ⭐ Official API docs: ⭐ [https://github.com/BohemianCoding/SketchAPI/blob/develop/docs/api/Rectangle.md](https://github.com/BohemianCoding/SketchAPI/blob/develop/docs/api/Rectangle.md)

- Exporting layer data to XML and JSON 👌: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-6-exporting-data-36865e571122](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-6-exporting-data-36865e571122)

- Comprehensive plugin example: [https://github.com/sonburn/symbol-instance-locator](https://github.com/sonburn/symbol-instance-locator)

- Simple example: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-1-28a5e3f01c68](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-1-28a5e3f01c68)

- Import other scripts: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-3-writing-reusable-code-b0200dba7d6c](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-3-writing-reusable-code-b0200dba7d6c)

- More advance javascripting for sketch: [https://benfrain.com/creating-a-sketch-plugin-with-javascript/](https://benfrain.com/creating-a-sketch-plugin-with-javascript/)

- Has lots of sketch.api calls: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-5-the-mslayer-class-1868c5b6e1e8](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-5-the-mslayer-class-1868c5b6e1e8)

- Applying color style to an entire doc hierarchy and has import/export code: [https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-7-creating-a-shared-color-palette-9c0a1899ee02](https://medium.com/@marianomike/the-beginners-guide-to-writing-sketch-plugins-part-7-creating-a-shared-color-palette-9c0a1899ee02)

- Center and scaling a shape: [https://github.com/turbobabr/Sketch-Plugins-Cookbook#8-scaling-layers](https://github.com/turbobabr/Sketch-Plugins-Cookbook#8-scaling-layers)

- Some file manipulation methods: [https://gist.github.com/abynim/04fd575a7e63ae2908a9](https://gist.github.com/abynim/04fd575a7e63ae2908a9)

- Writing JSON: [https://gist.github.com/aaronash/054cc8955420ebeaee90](https://gist.github.com/aaronash/054cc8955420ebeaee90)

- Overview of type constants: [https://github.com/turbobabr/sketch-constants/blob/master/src/index.js](https://github.com/turbobabr/sketch-constants/blob/master/src/index.js)

- Has all the hidden API calls in Sketch: [https://github.com/abynim/Sketch-Headers/tree/master/Headers](https://github.com/abynim/Sketch-Headers/tree/master/Headers)   

- Using Nib as alert root : [https://github.com/skpm/nib-loader/blob/master/README.md](https://github.com/skpm/nib-loader/blob/master/README.md)

- Writing a sketch plugin with webview: [https://www.smashingmagazine.com/2017/08/create-sketch-plugin-front-end-technologies/](https://www.smashingmagazine.com/2017/08/create-sketch-plugin-front-end-technologies/)

- Nice simple boilerplate plugin setup: [https://github.com/marianomike/sketch-sharedcolorpalette/tree/master/SharedColorPalette.sketchplugin/Contents/Sketch](https://github.com/marianomike/sketch-sharedcolorpalette/tree/master/SharedColorPalette.sketchplugin/Contents/Sketch)

- Lots of sketch plugins: [sketchpacks.com](sketchpacks.com) 
