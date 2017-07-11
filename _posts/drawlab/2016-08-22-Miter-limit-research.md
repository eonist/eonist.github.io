/*miter-limit*/
/* Quartz divides the length of the miter by the line width. If the result is greater than the miter limit, Quartz converts the style to a bevel.*/
/*

Quartz miter limit:
The default Quartz miter limit is 10. This value causes miter joins to be replaced by beveled joins when the angle between the segments is less
than about 11 degrees. A larger miter limit allows the miter length to be larger, that is, allows larger spikes. 
Figure 6.14 shows the results of the miter limit. In the lower por- tion of the figure, the miter limit is the default value of 10. 
As the angle between the two connected path segments gets smaller, the miter join is replaced by a beveled join. 
The upper portion of the figure shows a large miter limit with a small angle between the two connected path segments; the sharp spike is the
result of a large miter length. Although the function CGContextSetMiterLimit adjusts the miter limit, the default value is appropriate for most applications.

*/


/*

SVG miter limit:
For example, a miter limit of 1.414 converts miters to bevels for theta less than 90 degrees, 
a limit of 4.0 converts them for theta less than approximately 29 degrees, 
and a limit of 10.0 converts them for theta less than approximately 11.5 degrees.

svg miter limit is calculated like this:

When two line segments meet at a sharp angle and miter joins have been specified for stroke-linejoin, 
it is possible for the miter to extend far beyond the thickness of the line stroking the path. 
The stroke-miterlimit imposes a limit on the ratio of the miter length to the stroke-width. 
When the limit is exceeded, the join is converted from a miter to a bevel.

The ratio of miter length (distance between the outer tip and the inner corner of the miter) to stroke-width is 
directly related to the angle (theta) between the segments in user space by the formula:

miterLength / stroke-width = 1 / sin ( theta / 2 )

*/
//TODO: You have information about how to calculate the correct miter limit, either in the outline code or in the Quartz notes