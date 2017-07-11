My notes on Infinite tree list <!--more--> 

### All together now 🎉: 

<img width="180" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/elastic_fast_tree_list.mov.gif">

It's fast and smooth now. Next is to add some utility methods. explodeAt, collapseAt etc

### Uni-Directional Elastic Tree-List:

<img width="174" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/infinite_tree_liste.mov.gif">

- Bigger tree list with Speedier scrolling is coming in a few days. Need to make SVG Asset reload lazy. And Add rotation to the CSS lib. To make everything fast. 

### Uni-Directional Elastic List:

<img width="154" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/UniElasticList.mov.gif">

The above Gif anim shows A FastList that that can be scrolled in x and y dir. this is imp with treeLists as you need to scroll in the x-axis if the items becomes too wide. The deeper you go the more you need to scroll in the x-axis etc. 

```swift
//Then you start designing Infinite treeList 
    //differentiate indentation via css-id-classifier 👍
        //you prob need classId for this, but use element-id for first interpolation 🚫 (not in the start)
    //when you open an item, you basically insert items into a flatList 🚫
        //you set the css-classifer to the correct indentation 🚫
    //when you hide children, you basically remove items from a flatList 🚫
    
    
//Basically you could Just use FastList with a treeList DP. 👍
    //if you click on an item open/close icon, you change the dp. and the dp changes FastList 👍
    //The TreeListDP will set the indentation by changing the css-id-selector 👍
```

- You also need to implement The side scroller. And make a sensible solution for Views that can scroll both ways. ✅
- Thinking about it, this will easily enable filtering the TreeList through search 💯

- FastList that can scroll x/y. basically you just scroll the contentContainer and render takes care of placing items in y ✅
- You could also just move the items.x when x scroll is detected ✅

```swift
//Tasks:
	//Make ElasticView again with v2 scrolling code ✅
		//test ScrollView2 ✅
		//test ElasticView2 ✅
		//make the ElasticView as small as possible ✅
		//make call everything ...2 to differentiate (duplicate utils code if needed) 
	//Make List that can scroll both ways. ✅
	//Make fastList that can scroll both ways ✅
		//the problem occurs when you want a sideScrolling list. I guess this can be toggled via bool flag. ✅
			//The Container view doesn't need dir, only list needs dir. as containerview is x/y directional ✅
	//Maybe all lists and all views need x/y sliders, you just disable them when there is enough views ✅
		//Make the code foot print as small as possible ✅
```