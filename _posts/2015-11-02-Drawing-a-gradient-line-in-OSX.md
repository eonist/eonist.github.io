There are a couple of ways to do it<!--more--> :

1. CGContextReplacePathWithStrokedPath(context) (==This seems to be the correct answere==) // which creates a sort of outline og the stroke that you fill  
[here](http://stackoverflow.com/questions/1303855/how-to-draw-a-gradient-line-fading-in-out-with-core-graphics-iphone) , [here](http://stackoverflow.com/questions/20632365/draw-gradient-along-a-curved-uibezierpath) and the best:  [here](http://stackoverflow.com/questions/22042717/gradient-for-the-curved-line-drawn-with-quartz-2d) 

2.  CAGradientLayer to get the gradient effect, and use the CAShapeLayer as a mask. (backdraw: uses layer)  
[here](http://stackoverflow.com/questions/20630653/apply-gradient-color-to-arc-created-with-uibezierpath) 

3. Use blend mode and set the line -stroke as source-in  (reports of slow performance)  
[here](http://stackoverflow.com/questions/2366571/applying-a-texture-to-stroke-a-cgcontextstrokepath) 

4. CGContextClip(context)  can also be used (masking i guess)  
its mentioned [here](http://stackoverflow.com/questions/1303855/how-to-draw-a-gradient-line-fading-in-out-with-core-graphics-iphone) 

5. A bit of a stretch but might work: Graham Cox's excellent GCDrawKit has a -strokedPath  
CGContextReplacePathWithStrokedPath() method to retrieve a path that is the outline of the stroked path. Graham Cox's excellent GCDrawKit has a -strokedPath category method on NSBezierPath that will do this for you without needing to drop down to Core Graphics.


**So what I'm thinking** is that I start with the Quartz books and try to make shapes etc, then make examples of masking, different lines, then make utility methods for all of this to have it all in a convenient place. You need to dig down to core graphics anyway at some point so just do it now already. Start small and make examples

**Note:** the CGContextReplacePathWithStrokedPath method it ~~also uses a gradient that clips the path~~. Here is the basic setup:

```objc
 const CGContextRef context = UIGraphicsGetCurrentContext();

    // Define your stroked path. You can set up anything you like
    // here.
    CGContextAddRect(context, yourRectToStrokeWithAGradient);

    // Set up any stroking parameters like line width (or dashing).
    CGContextSetLineWidth(context, 1);

    // Use the magic call to create a fillable path.
    CGContextReplacePathWithStrokedPath(context);

    // Turn the fillable path in to a clipping region.
    CGContextClip(context);

    // Draw the gradient (it will clip to the path).
    CGContextDrawLinearGradient(
      context, 
      yourGradient, 
      gradientTop, gradientBottom, 
      0);
```

**Here is another setup for CGContextReplacePathWithStrokedPath: (that doesnt use the CGContextClip):**  


```objc
CGPoint point1 = CGPointMake(50, 70);
CGPoint point2 = CGPointMake(70, 75);

bool drawACurve;
drawACurve = true;

float tangent;
if ((int)(point2.y - point1.y) == 0) {
    tangent = 0.0f;
    drawACurve = false;
}
else {
    tangent = (point2.x - point1.x) / (point2.y - point1.y);
}

UIBezierPath *path1 = [UIBezierPath bezierPath];
[path1 setLineWidth:1.0];

if (drawACurve)
{
    // Draw a curve
    float factor;
    CGPoint controlPoint1;
    CGPoint controlPoint2;

    factor = MAX_FACTOR;

    if (fabs(tangent) >= 1.0 && fabs(tangent) < TANGENT_BOUND_FOR_LINE)
        factor = MIN_FACTOR;

    if (tangent > 0) {
        controlPoint1 = CGPointMake(point2.x - point2.x * factor, point1.y - point1.y * factor);
        controlPoint2 = CGPointMake(point1.x + point1.x * factor, point2.y + point2.y * factor);
    }
    else {
        // For tangent less than zero
        controlPoint1 = CGPointMake(point2.x - point2.x * factor, point1.y + point1.y * factor);
        controlPoint2 = CGPointMake(point1.x + point1.x * factor, point2.y - point2.y * factor);
    }

    [path1 moveToPoint:point1];
    [path1 addCurveToPoint:point2 controlPoint1:controlPoint1 controlPoint2:controlPoint2];
}
else
{
    // Draw a line
    [path1 moveToPoint:point1];
    [path1 addLineToPoint:point2];
}

CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB(); // You could choose another color space.
CGFloat locations[2] = {0.f, 1.f}; // Locations for a simple linear gradient with a start color and end color at either end of the drawing points.
CGFloat colorComponents[8] = {0.f, 0.f, 1.f, 1.f, // Start blue
                                0.f, 1.f, 0.f, 1.f}; // Finish green
CGGradientRef gradient = CGGradientCreateWithColorComponents(colorSpace, colorComponents, locations, 2); // Create the gradient. Can also be created with CGGradientCreateWithColors().
CGColorSpaceRelease(colorSpace);

CGPathRef outerPath = CGPathCreateCopyByStrokingPath(path1.CGPath, NULL, path1.lineWidth, path1.lineCapStyle, path1.lineJoinStyle, path1.miterLimit); // This function creates a path that outlines another, using various stroke options. Pass in the stroke options from path1.
// From the docs: The new path is created so that filling the new path draws the same pixels as stroking the original path.

CGContextRef context = UIGraphicsGetCurrentContext(); // Get the current graphics context.
CGContextAddPath(context, outerPath); // Add the new path.
CGContextClip(context); // Clip the context to the path.

CGRect boundingBox = CGPathGetBoundingBox(outerPath); // I'm just doing this to get the startPoint and endPoint for the gradient drawing.
CGPathRelease(outerPath);
CGPoint startPoint = boundingBox.origin;
CGPoint endPoint = CGPointMake(boundingBox.origin.x, CGRectGetHeight(boundingBox)); // Different from startPoint in y value, as requested.

CGContextDrawLinearGradient(context, gradient, startPoint, endPoint, 0); // Draw the gradient.
CGGradientRelease(gradient);
```