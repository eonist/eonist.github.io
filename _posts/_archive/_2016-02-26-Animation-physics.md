A simplistic overview of motion physics<!--more-->
 
- 1. [Example](#example) 
- 2. [Notes](#notes) 
- 3. [Tasks](#tasks) 

## Example:

## Notes:

**Velocity** (speed) :  
`velocity = (distance to target) / (time * fps)` (velocity equals distance traveled per frame tick) 

**Easing**
`velocity = (distance to location) * easing` (Easing can be 0.85 aka ease-out, or 1.25 ease-in)

**Gravity**  
`yVelocity += 5` (gravity pulls an object faster and faster towards the ground)

**Friction**
`velocity *= 0.98`  subtle friction  (0.98 = the friction value)

**Springiness**
`velocity * 0.1` Spring is sort of the acceleration (Think metal coil)

**Bounciness**
Objects can bounce off other objects or boundaries like borders. 

**Bouncing of borders**
When an object hits a border, you need to:  
- Check if the object is within hitting the border (min max within this border, if the border is a box min max the x,y,width,height)
- Figure out the trajectory of the object towards the border normal. Basically use the last known position of the object and the cur pos to create a trajectory vector. 
- Use the trajectory vector to calculate the angle on the border line. Then take the normal of the border line and mirror the angle to the other side of the normal. Think ball hitting a wall.
- Set the new x and y velocity to the angle of the new trajectory (basically all angles has a value for x and y use these)

**Bouncing of other Objects**
You can calculate exit trajectories when two objects hit each other by adding the velocity of one object to the other. (this applies for both objects) in simplistic form: `a.vel += b.vel and b.vel += tempA.vel` 



## Tasks:
- [ ] Do the chaining demo on page: 207 in the Foundation anim book
- [ ] Do the multi spring demo as well on p.212 Foundation anim book