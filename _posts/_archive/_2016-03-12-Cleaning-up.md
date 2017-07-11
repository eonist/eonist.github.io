Cleaning up the framework<!--more--> and Squashing bugs etc

<img width="1055" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-03-17 at 21.10.40.png">

[Mavericks CSS](https://github.com/stylekit/mavericks)  
[Explorer.app](https://github.com/stylekit/explorer) 

## Tasks
- [x] Start a new repo for faster compile times?
- [x] The specificity engine isn't fine tuned enough. Keep at it
- [x] add the TextButton to The Explorer
- [x] ==try translucent bg for the explorer==
- [x] Fix the bug with the filter array method that excludes a series of variables from directly inheriting
- [x] write a test that proves that the direct inheriting is fixed
- [ ] add support for multiple css variables pointing to the same value. Aka siblings. See legacy code
- [ ] elements should float left and clear left by default
- [ ] add isFocused and isDisabled to all components
- [ ] ==multi layered elements should work with single valued css variables==
- [ ] fix the hit-test problem with some buttons. Make a test to solve it once and for all.
- [ ] create an article on how buttons behave
- [ ] Fix the css Bracket grouping, and if it is fixed, make sure single items can still work inside a group
- [ ] stopwatch the css parsing, see how much time it uses compared to the legacy code
- [ ] Add a press-kit katalog on dropbox like this: [here](https://www.dropbox.com/sh/fxnjjtmprzgp1le/AADMaNPCNr3RpVSvVi59Tyvaa?dl=0) 
- [ ] The svg icon in "The Search box component" cant have a custom color. Figure out why with a test. 
- [ ] The VSlider needs some work, it doesn't align correctly etc. Do this after you have added the lists etc
- [ ] Fix the problem where you cant retrieve size values from a single variable when using css layers
- [ ] CheckBoxButton extends Button, It should extend TextButton. Fix this
- [ ] RadioButton doesn't accept NaN values when init. CheckBoxButton supports this so fix it
- [ ] Fix the SelectButton.isSelected variable. Use getSelected() similar to how CheckButton works
- [ ] Fix sibling style selectors so that ColorTag.css can be written simpler. 
- [ ] Fix ColorTag selected state. Currently it inherits from RadioBullet styles 
- [ ] implement that fill can be none `fill:none;` Currently it throws an error
- [ ] Implement custom line with the AssetDecorator, in conjunction with the SVGAsset scheme
- [ ] Stroke-width should scale with the resizing of SVGAssets. (use division to find appropriate stroke-width)
- [ ] When you set `line-offset-type:inside;` and `corner-radius:5px;` The result is just a rect. Outside works. 
- [ ] SVGEngine should support viewport variables. 
- [ ] The css parser has some problems with trailing whitespace after the `;` char
- [ ] Is NaN values really important? What is their purpose? 
- [ ] Align the List Items pixel perfect. There is a padding or margin bug in the css