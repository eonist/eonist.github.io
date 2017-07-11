The floating algorithm takes care of floating the Elements to their position <!--more--> This is essentially the same as you find in regular CSS. You can also turn of floating on elements you want to control with swift code. Like a scrollbar slider. Or graph elements in a graph. 

**Content of page:**
1. [Examples](#examples) 
2. [Thoughts](#thoughts) 
3. [Tasks](#tasks) 

### Examples
The floating algorithm at work: (2 boxes in a Section that both are set to float:left and clear:left)
<img width="391" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-26 at 23.57.16.png">

`float:left;clear:left;`
<img width="610" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-27 at 07.05.23.png">

`float:right;clear:right;`
<img width="610" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-27 at 07.05.05.png">

`float:left;clear:none;`
<img width="610" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-27 at 07.05.45.png">

`float:right;clear:none;`
<img width="610" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-27 at 07.06.03.png">



`float:none;clear:none;` All boxes are on top of each other, Use swift code to position, and or animate.
<img width="610" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-01-27 at 07.11.44.png">

### Thoughts: 
- I think we should redesign the Window element to act more independently, the window should act as an element and just use the view as a vessel for the content. test if this is possible of course
- Window cant be a view so we have to use the getParent(isAbsolute:Bool = true) to get the view to work on.

### Tasks:
- [x] Complete the algorithm
- [x] Start testing with boxes
- [ ] Add more complex test cases
- [ ] Create a nice animation showcasing how the floating works in different scenarios
- [ ] Test resizing
- [ ] Test center aligned
- [ ] Add support for default floating and clearing values
- [ ] When testings are solid, solve the "hit area for skin elements" and then Start making the rest of the components 