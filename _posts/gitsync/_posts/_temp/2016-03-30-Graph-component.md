Making of a Graph component<!--more-->

## Brainstorm-log:

- You could use the SVG drawing lib. Or the graphics kit.
- Your data set will have x,y where x is time and y is values
- It will need to be gesture zoomable in the future.
- The data set will be a simple array of numbers which would represent the y-axis and the indices in the array would represent the x-axis (time) 
- To figure out the init y-axis (value) min and max, you need to get the min and max from the data-set of values. 
- Say if you wanted to build a graph over the commit rate over a day. The x axis would represent 0-24 hours. at a 12 interval point grid, so at every 2 hour interval. 
- To create a useable 12 indices array of values from commits in a project you would need to query the repo for all commits within the last 24 hours. Basically from 00:00 until 24:00. Then you would store all commits in an array. Then you would iterate over the entire array and iterate an array with 12 values of 0. So if commit was made 12:01 it would mean that index 6 would be iterated once. All this could be automatically calculated in a for-loop. (debug this loop in playground)
- Once you have the value array. You then use the min max values from the value array to create the y-axis interval. So if in one hour you have 5 commits this will be the max height of the y-axis. 
- If the max value some arbitrary number like 13. How do you make the value indicators? You need some sort of rounding system. 76 rounds to? Basically use Modulate somehow. 100-76 = 24  . So 76 is shorter from a 3-digit value than a it self as a 2 digit num. 55 is 45 away from 3-digit and 55 away from it self. 33 is closes to it self than to 100. 
- So then you just take the 3-digit value and half it to 50 if the value is closer to it self. Then you set 50 as the top value in the y-axis. 
- For values such as 176. You use the 3-digit approach But with 200-176. and 100-76. to figure out which num is closest. 
- Basically digit analytics at work here. Figure it out in playground.
- Once you have the max indicator value you then try to best divide this by either 2,3,4,5
- To draw the GraphPoints you simply get the scalar value of the y-axis value, and multiply that by the height of the graph component. You do the same with the GraphPoint.x value. (Scalar of time) * (GraphComponent.width) 
- To update the GraphPoints you simply re-iterate over the dataset and change x,y values of each GraphicPoint
- The GraphLine contains the GraphPoints. The GraphLine component also has a LinePath That is behind the GraphicPoints
- All the UI components in the Graph should be Skin-able via css. So you may need to add support for the LinePath
- You could do this via the SVGKit but Then it wouldn't be easily skin-able via css.
- When you hover the GraphicPoint a Popup should be shown with info about the number of commits at this point. (fade-in on hover fade-out on hover out)
- The X,Y axis Indicator lines and numbers should be Element components

## Tasks:
- [ ] Experiment with the Digit analytics kit. 
- [ ] Create the GraphPoint and place it in the Graph component and test
- [ ] Add the LinePath as a skin-able component
- [ ] Build out the y-axis-bar and the x-axis-bar (See drawlab) 