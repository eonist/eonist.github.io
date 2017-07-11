My notes on easing<!--more--> 

## Animator notes:
Seek,reverse,pause,repeat,autoReverse

From facebook pop (inspiration for motion names)
```objc
_anim.fromValue = @(0.0);
_anim.toValue =  @(100.f);
_anim.springBounciness = 20.1;
_anim.springSpeed = 20.4;
_anim.dynamicsTension = 200;
_anim.dynamicsFriction = 100.f;
_anim.dynamicsMass = 1.f;
_anim.property = self.pop;
//[shape pop_addAnimation:_anim forKey:nil];
```

Maybe You can use CAMediaTimingFunction as a function for easing?  if you figure out how to use them
```objc
anim.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut];//
anim.fromValue = @(0.0);
```


**Custom Easing methods**  
https://github.com/intuit/AnimationEngine/blob/master/INTUAnimationEngine/INTUEasingFunctions.h


**Spring anim names**  
- damping: The amount of friction. Must be greater than or equal to zero. If exactly zero, the harmonic motion will continue indefinitely. Typical range: 1.0 to 30.0
- stiffness: The stiffness of the spring. Must be greater than zero. Typical range: 1.0 to 500.0
- mass: The a


**Visualizing of easing:** 
	http://easings.net

**Here are the easing methods: in code:**   
https://github.com/intuit/AnimationEngine/blob/master/INTUAnimationEngine/INTUEasingFunctions.m


## Robert Penner easing

```swift
func noEasing (t, b, c, d) {
	return c * t / d + b;
}
```

t: time
b: begin
c: change
d: duration


## Peripheral links:
- Creative easing formulas: http://sol.gfxile.net/interpolation/

## Tasks
- [ ] Add multiple easing methods