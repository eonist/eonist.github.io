## RBSliderThumb

1. Values from 0 to 1 should represent the slider moving from top to bottom.

2. Values outside these values should represent scaling of the thumb while pinning to top or bottom. 

3. Create a method that calculates the overshot of the slider in relation to the scalar value. 

4. To debug this you should make a slider that can go from -0.5 to 1.5

5. Keep in mind that the SliderThumb needs to realign when the pageSize changes, maybe make a test for this first?

6. never mind the above ideas, just use 0-1 scalar values, but also include an overshot scalar value. which should scale an inner value of the thumb, or a temp variable to keep styles simple. 

7. use 2 over shot scalar values one for the top and one for the bottom. use realY to store and YVal to differentiate the values. only resize if using the topOvershot

8. this will make it easier to implement directly an also to implement legacy code for page resize support

9. create an Internal Thumb class
10. it should resize and setPosition of its own skin for given overshoot values. 
11. so -0,25 top overshoot then resize its own height to height-(height*abs(topOvershot))
12. do the same with the bottomOvershot but also offset the skin.y value with the same amount
13. do a isolated button test with this scenario first.