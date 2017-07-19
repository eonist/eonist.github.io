My notes on custom "interruptible" animation in swift <!--more--> Aka the holy grail of swift animation ref this BigSpaceShip [article](https://blog.bigspaceship.com/the-holy-grail-of-ios-animation-intervals-e9e663ea93c4) 

#### Springing:

When springing, the acceleration is proportional to the distance. If the item is far away from the target, a lot of acceleration is applied, increasing the velocity quickly. As the object gets closer to its target, less acceleration is applied, but it’s still accelerating! It flies right past the target, and then acceleration pulls it back. Eventually, friction causes it to settle down.

```swift
func progress(value:CGPoint){/*This method gets called 60FPS, add the values to be manipulated here*/
    disableAnim {/*Important so that you don't get the apple "auto" anim as well*/
        ellipse.graphic.layer?.position = value
    }
}
let animator = PointSpringer(progress, PointSpringer.initValues,PointSpringer.initConfig)/*Setup interuptable animator*/
func onViewEvent(_ event:Event) {/*This is the click on window event handler*/
    if event.type == ButtonEvent.upInside {
        animator.targetValue = bg!.localPos()/*Set the position of where you want the anim to go*/
        if animator.stopped {animator.start()}/*We only need to start the animation if it has already stopped*/
    }
}
bg?.event = onViewEvent
```
<img width="394" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/spring.mov.gif">


To explain how springing works, let’s simulate it with some sample numbers. Let’s say the x position is 0, vx
is 0, the target x is 100, and the spring variable is 0.1. Here is how it might progress:
```
1. Multiply distance (100) by spring, and you get 10. Add that to vx, which then becomes 10. Add
velocity to position, making the x position 10.
2. Next step, distance (100 – 10) is 90. Acceleration is 90 times 0.1, or 9 this time. This gets
added to vx, which becomes 19. The x position becomes 29.
3. Next step, distance is 71, acceleration is 7.1, which added to vx makes it 26.1. The x position
becomes 55.1.
4. Next step, distance is 44.9, acceleration is 4.49, and vx becomes 30.59. The x position is then 85.69.
The thing to note is that the acceleration on each frame becomes less and less as the object approaches its target, but the velocity continues to build. It’s not building as rapidly as it was on pre- vious frames, but it’s still moving faster and faster.
After a couple more steps, the object goes right past the target to an x position of around 117. The distance is now 100 – 117, which is –17. A fraction of this gets added to the velocity, slowing the object down a bit.

```
- A high spring value will make a very stiff spring. Something lower will look more like a loose rubber band. You can play around with the friction and spring values to get the look you want


#### Easing:
when you use easing to move an object into position, it automatically takes on a very natural appearance. One of the coolest things is that simple easing is actually very easy to do. In fact, it’s prob- ably easier than figuring out the angle, the vx, and the vy, and moving at a fixed speed.

```swift
//Use the Springer example but switch out the PointSpringer with PointEaser
```
<img width="398" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Easer_3.gif">

```
Here is the strategy for easing:
1. Decide on a number for your proportional motion. This will be a fraction of 1. 2. Determine your target.
3. Calculate the distance from the object to the target.
4. Multiply the distance by the fraction. This is your velocity.
5. Add the velocity value to the current position.
6. Repeat steps 3 through 5 until the object is at the target.
```


#### Final note:

There is also NumberSpringer and NumberEaser which can be used to manipulate CGFloat. Which enables you to animate color transition. Rotation, shadow, gradient, 3d perspectives or any other variable. unlike apples built in animation system this Animation lib enables you to animate any property you desire. You can also Extend the Easer or Springer class with your own Custom class so that it can have more custom logic. Say you want to do something with Point3D and need to account for the z value as well. The possibilities are endless.

The AnimLib is Open-source MIT and can be downloaded [https://github.com/eonist/swift-utils](https://github.com/eonist/swift-utils) 