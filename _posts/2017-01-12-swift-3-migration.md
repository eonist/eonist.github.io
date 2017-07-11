After manually migrating 35.000 lines of swift 2.2 into swift 3.0 code. <!--more--> Here are some insights:  

## Range:
Range in swift 3 has been totally re-designed. 🙈   
We now have 2 Main Range types: Range and CountableRange.   
Apples motivation for separating these types was:    

1. Make a light-weight range type that only holds 2 Ints (start and end)  

2. Make a heavy-weight range type that holds a copy of the original Collection type (aka Array)  
  
**Important:** If you want to create extensions for Range you now have to make extensions for: Both types as they don't inherit a common protocol    

**Important:** Previously the generic item type for ranges was Element in swift 3 this is called Bound  

**Important:** 0...4 is now called a: Closed​Range and CountableClosedRange the later if the Range contains a collection  

## For-loop:

The one c-style for-loop to rule them all is gone, now we have 10 different to take it's place: 

- ``for i in 0..4{}`` 👈 regular forward looping
- ``for (i,obj) in arr.enumerated(){print(i);print(obj)}`` 👈 access to i and obj
- ``for obj in arr{}`` 👈 iterate over objects (no access to original array)
- ``for i in (0..<4).reversed(){}`` 👈 backward looping
- ``var i = 0;while(i<4){print(i);i+=1}`` 👈 If you want to manipulate i while looping
- ``for i in stride(from:0,to:10,skip:2){}`` 👈 Skips every other
- ``arr.forEach{$0}`` 👈 Easiest for-loop but only if you don't need to exit early
- ``for i in arr.indices {print(i)}`` 👈 Access to i
- ``for _ in 0..<arr.count`` 👈 If you just wan't to loop something and not use any value
- ``arr.reversed.forEach{$0}`` 👈 reversed forEach, more functional 🤖 .map also works
    
    
## NSView:

``drawLayer(layer:CALayer, inContext ctx: CGContext)`` 👈 This has vanished with out a trace to work around build it your self or? 
  
``actionForLayer(layer:CALayer, forKey event: String) -> CAAction?`` 👈 Also gone, Solution: build it your self or?

**Important:** Seems these methods are apart of Metal now. So instead of extending NSView we now: ``Import MetalKit`` and ``CustomView:MTKView``   
**IMPORTANT:** MetalKit is revolutionary but easy to use out of the box it is not. A simpler solution is to implement CALayerDelegate that has drawLayer and actionForLayer. And then implement these your self.  🔑 Instructions for this [here](https://developer.apple.com/reference/quartzcore/calayerdelegate/2097261-display)   

## Modulo:

**Bring back the simple modulo syntax in swift 3:**

This syntax was actually suggested on Apples official swift mailing list [here](https://lists.swift.org/pipermail/swift-evolution/Week-of-Mon-20160523/018679.html)  but for some reason they opted for a less elegant syntax.  

```swift
infix operator %%/*<--infix operator is required for custom infix char combos*/
/**
 * Brings back simple modulo syntax (was removed in swift 3)
 * Calculates the remainder of expression1 divided by expression2
 * The sign of the modulo result matches the sign of the dividend (the first number). For example, -4 % 3 and -4 % -3 both evaluate to -1
 * EXAMPLE: 
 * print(12 %% 5)    // 2
 * print(4.3 %% 2.1) // 0.0999999999999996
 * print(4 %% 4)     // 0
 * NOTE: The first print returns 2, rather than 12/5 or 2.4, because the modulo (%) operator returns only the remainder. The second trace returns 0.0999999999999996 instead of the expected 0.1 because of the limitations of floating-point accuracy in binary computing.
 */
public func %% (left:CGFloat, right:CGFloat) -> CGFloat {
    return left.truncatingRemainder(dividingBy: right)👈🙈
}
```

## Boolean:

```swift
/**
 * Swift 3 removed the possibility to cast CGFloat to Bool. This method brings back this functionality.
 * JUSTIFICATION: Most other languages allow this functionality, and is familiar to the user the alternative is verbose code. Which makes code cognitively harder to read.
 * NOTE: Bool(Int(1)) still works natively
 * EXAMPLE: (expected results from other languages)
 * Bool(CGFloat(0))//false
 * Bool(CGFloat(-2))//true
 * Bool(CGFloat(20))//true
 * Bool(CGFloat.nan)//true
 */
extension Bool{
    init(_ value:CGFloat) {
        self.init(value != 0)
    }
}
```
  
## CGPath:
public CGPath methods must be converted manually:  
- ``CGPathAddPath(self, nil, path)`` is now: ``self.add(path)`` etc  
- ``CGAffineTransformIdentiy`` is now: ``CGAffineTransform()``
- ``CGPathAddCurveToPoint(cp1,cp2,point)`` is now: ``path.addCurve(point,cp1,cp2)`` 👈 Notice that point has changed place. 


## CGRect:
CGRect seems to be a struct now (aka value type) and some methods are removed as a result, But you can get them back:    
- ``CGRect().offsetInPlace()`` 👈 has been removed. Solution is to make an extension that implements the functionality again.   

## NSDate:
NSDate is now Date:  
- ``NSCalendar.currentCalendar().dateByAddingUnit()`` is probably: ``Calendar.current.date()`` 👈 The syntax doesn't include options. So I think this needs some extra research.  

## AnyClass:
``.isMemberOfClass`` is now: ``isMember(of:)``  

## Selector
was: ``ObjectiveC.Selector("onMagnifyGesture:")`` now: ``#selector(onMagnifyGesture)``

## Other tidbits:
XCode now complain if a method that returns a var is unused:   
``_ = someReturningMethod()`` 👈 The solution is to add: ``_ =`` infront of the call  


## Final notes:
- If you download the latest XCode via MacOS appStore, it will update your old XCode. A better solution is to find the direct link via apple.com and download it manually. This way you can have two versions of XCode on your computer. It's important to have 2 versions of XCode when you migrate, so that if you get into trouble during the migration you can always revert and try to figure out what worked in your previous swift 2.0 code.   

- To get 7x BuildTime Speed decrease going from swift 2.0 to 3.0: set this variable: SWIFT_WHOLE_MODULE_OPTIMIZATION = YES [More info here](http://www.splinter.com.au/2016/11/06/swift-3-migration/) 👈 Take this advice with a pinch of salt. But its worth trying.