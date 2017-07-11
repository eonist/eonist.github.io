Chamfering (Fillet) of a corner or a point (two connecting lines)

- Needs support for 2 lines, (future support for 2 bezier lines, 2 arc lines,) 

_ Drag a point to desired chamfer with a chamfer tool. Or set the chamfer via a window panel with spinners and numeric input, must have preview toggle button. 

- When you reach the maximum of one of the lines you should not be able to chamfer anymore
with 2 lines , the chamfer will stop. 

- The chamfer tool should display additional options in the dynamic tool panel:
snap at every 20px radius of the chamfer.

- Find the math you need in your notes

with two lines you can create the chamfer by trying to fit a circle with the radius of choosing.

- You need to find out how much of the lines are eaten at specific radiuses. So you can stop when you reach max. and so that you can manipulate the lines when in preview mode.

- the panel should have radius spinner, preview toggle button, from 0 to the max radius possible

- the chamfer should support multiple point selections. thus the max values are effected. and must be taken into account. 


**Thoughts on multi-corner-chamfering**

- store the selected indices 
- delete the selected point
- add lineTo to the left side 
- add arcTo to the right side
- increment the indices by 1 after every iteration to find the next selected point.
- or loop backwards over the indices and remove and add lineTo and arcTo as needed

Note:
This won't be as elegant as the current solution, but the current solution isn't perfect

**Further thoughts on multi-corner-chamfering:**

//if 2 selected CP are on the same line
 //find maxRadius, with the new maxRadius method
 //chamfer until maxRadius is reached
 
//you need to loop over the selected corners (once)
	//find selectedCP that are connected by the same line, calculate the maxRadius
	//isolated selected CP's should have their max radius calculated by finding the smallest leg
	//store the smallest maxRadius

//the smallest maxRadius is important when you change the radius of all selected corners

//you need to chamfer each corner backwards in the loop. or else you will lose selected CP indecies

//When the chamfering is complete (clean up)
	//run backwards over the CP's 
		//if 2 CP's that are next to each-other are near-equal in position then merge them together 
		
		
//Test all this with variouse test (squares, complex polyLines, triangles open and closed etc)
 