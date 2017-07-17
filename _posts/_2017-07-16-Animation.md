Animation in swift <!--more--> 

#### Sprining:

In springing, acceleration is proportional to the distance. If the object is far away from the target, a whole lot of acceleration is applied, increasing the velocity quickly. As the object gets closer to its target, less acceleration is applied, but it’s still accelerating! It flies right past the target, and then acceleration pulls it back. Eventually, friction causes it to settle down.

<img width="394" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/spring.mov.gif">

- A high spring value will make a very stiff spring. Something lower will look more like a loose rubber band. 

#### Easing:
when you use easing to move an object into position, it automatically takes on a very natural appearance. One of the coolest things is that simple easing is actually very easy to do. In fact, it’s prob- ably easier than figuring out the angle, the vx, and the vy, and moving at a fixed speed.

```
Here is the strategy for easing:
1. Decide on a number for your proportional motion. This will be a fraction of 1. 2. Determine your target.
3. Calculate the distance from the object to the target.
4. Multiply the distance by the fraction. This is your velocity.
5. Add the velocity value to the current position.
6. Repeat steps 3 through 5 until the object is at the target.
```
<img width="398" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Easer.mov.gif">