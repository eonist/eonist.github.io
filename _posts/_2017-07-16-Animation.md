My notes on Custom Animation in swift <!--more--> 

#### Sprining:

In springing, acceleration is proportional to the distance. If the object is far away from the target, a whole lot of acceleration is applied, increasing the velocity quickly. As the object gets closer to its target, less acceleration is applied, but it’s still accelerating! It flies right past the target, and then acceleration pulls it back. Eventually, friction causes it to settle down.

Before you write any code, let’s simulate it with some sample numbers. Let’s say the x position is 0, vx
is 0, the target x is 100, and the spring variable is 0.1. Here is how it might progress:
```
1. Multiply distance (100) by spring, and you get 10. Add that to vx, which then becomes 10. Add
velocity to position, making the x position 10.
2. Next round, distance (100 – 10) is 90. Acceleration is 90 times 0.1, or 9 this time. This gets
added to vx, which becomes 19. The x position becomes 29.
3. Next round, distance is 71, acceleration is 7.1, which added to vx makes it 26.1. The x position
becomes 55.1.
4. Next round, distance is 44.9, acceleration is 4.49, and vx becomes 30.59. The x position is then 85.69.
The thing to note is that the acceleration on each frame becomes less and less as the object approaches its target, but the velocity continues to build. It’s not building as rapidly as it was on pre- vious frames, but it’s still moving faster and faster.
After a couple more rounds, the object goes right past the target to an x position of around 117. The distance is now 100 – 117, which is –17. A fraction of this gets added to the velocity, slowing the object down a bit.

```
- A high spring value will make a very stiff spring. Something lower will look more like a loose rubber band. 
<img width="394" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/spring.mov.gif">



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

<img width="398" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Easer_3.gif">

