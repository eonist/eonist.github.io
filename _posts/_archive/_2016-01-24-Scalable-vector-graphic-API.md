Overview of the SVG properties that are implemented in my SVG lib. <!--more--> 

Content of page:  
1. [Line and shape](Line and shape) 
2. [Path general](Path general) 
3. [Ellipse-arc](Ellipse-arc) 
4. [style](style) 
5. [Fill](Fill) 
6. [Stroke](Stroke) 
7. [Units](Units) 

## Line and shape:
**M/m**
(moveTo) x,y

Establish origin at point specified

Two parameters (x,y) giving current position

**L/l**
(lineTo) x,y

Straight line path from current position to point specified

Two parameters (x,y) giving position of the line end point which becomes the current position.

**H/h**
(horizontalLineTo) x

Horizontal line path from current position to point specified

Single parameter giving X-coordinate of the line end point. 
The Y-coordinate is the same as that of the previous current position. The new point becomes the current position.

**V/v**
(verticalLineTo) y

Vertical line path from current position to point specified

Single parameter giving Y-coordinate of the line end point. 
The X-coordinate is the same as that of the previous current position. The new point becomes the current position.

**Z/z**
(closePath)

Straight line back to original point

No parameters.

**t/T**
Draw a quadratic Bézier curve from the current point to (x, y). The control point will be the reflection of the previous Q command's control point. If there is no previous curve, the currentt point will be used as the control point.

**a/A**
rx, ry, x-axis-rotation, large-arc, sweep, x, y (radii1 radii2, xrotation,  direction, direction, end point x, end point y) Note: the start point is where you move the curve to
Draw an elliptical arc from the current point to (x, y). The points are on an ellipse with x-radius rx and y-radius ry. The ellipse is rotated x-axis-rotation degrees. If the arc is less than 180 degrees, large-arc is zero; if greater than 180 degrees, large-arc is one. If the arc is to be drawn in the positive direction, sweep is one; otherwise it is zero.

**q/Q**
Draw a quadratic Bézier curve from the current point to (x, y) using control point (x1, y1).

**c/C**
x1 y1 x2 y2 x y
Draw a cubic Bézier curve from the current point to (x, y) using control point (x1, y1) as the control point for the beginning of the curve and (x2, y2) as the control point for the endpoint of the curve.

**s/S**
x2 y2 x y
Draw a cubic Bézier curve from the current point to (x, y), using (x2, y2) as the control point for this new endpoint. The first control point will be the reflection of the previous C command's ending control point. If there is no previous curve, the current point will be used as the first control point.

## Path general:
There are always two versions of the same drawing command :
in upper case : absolute coordinates (with respect to the overall coordinate system)
in lower case : relative coordinates

A basic form of a path looks like: 
 <path d=”M 0 0 L 100 100”>


The d attribute defines the path. It defines a path that consists of establishing a current position 
at the origin (Move to 0,0) and the path goes from there to the point (100,100) as a straight Line.

## Ellipse-arc

http://www.w3.org/TR/SVG/paths.html#PathDataEllipticalArcCommands

very imp stuff when calculating an arc : (center to end point parametrization or from endpoint to center parameterization, use full for exporting/importing)
http://www.w3.org/TR/SVG/implnote.html#ArcOutOfRangeParameters

d = The d attribute defines the path
m = move to (x,y) 
a = A (absolute) a (relative) arc
rx, ry two radii (rx, ry) (ellipse.XAxsis/2, ellipse.YAxsis/2)
R = x-axis-rotation (the rotatioin doesnt rotate start and end point, these need to be placed on the circumference from the start, the rotation rotates only the xRadii and the yRadii)
where “L,S” is  large-arc-flag and sweep-flag
<path d="M 125,75 arx,ry R L,S X,Y” style="fill:none; stroke:red; stroke-width:6"/>



**If large-arc-flag** is '1', then one of the two larger arc sweeps ￼ will be chosen; otherwise, if large-arc-flag is '0', one of the smaller arc sweeps ￼will be chosen,
**If sweep-flag is** '1', then the arc will be drawn in a "positive-angle" ￼ direction. A value of 0 causes the arc to be drawn in a "negative-angle" ￼ direction.


## style

fill-opacity:0.3

style="fill:red; stroke:purple; stroke-width:5; fill-opacity:0.3"
style="fill:rgb(0,0,255); stroke-width:1; stroke:rgb(0,0,0)"
style="fill: #ccffcc; stroke: green
style="stroke: black; fill: none;
style="fill:none; stroke:blue"
stroke="blue" fill="none"
fill="none" stroke="#008000"

Differend stroke ends:

butt
<line x1="30" y1="275" x2="70" y2="275"
style="stroke-linecap: butt; stroke: brown; stroke-width: 10"/>

round
<line x1="30" y1="295" x2="70" y2="295"
style="stroke-linecap: round; stroke: brown; stroke-width: 10;"/>

square
<line x1="30" y1="315" x2="70" y2="315"
style="stroke-linecap: square; stroke: brown; stroke-width: 10;"/>


corner style:

sharp corner
<polyline style="stroke-linejoin: miter; stroke: blueviolet; stroke-width: 14;
fill: none;" points="10 455, 25 440, 40 455"/>

rounded
<polyline style="stroke-linejoin: round; stroke: blueviolet; stroke-width: 14;
fill: none;" points="60 455, 75 440, 90 455"/>

beveled
<polyline style="stroke-linejoin: bevel; stroke: blueviolet; stroke-width: 14; 
fill: none;" points="110 455, 125 440, 140 455"/>

## Fill
**fill**
The fill color, as described in "Specifying Colors."

**fill-opacity**
A number ranging from 0.0 to 1.0; 0.0 is entirely transparent, 1.0 is entirely opaque.

**fill-rule**
This attribute can have the values "nonzero" or "evenodd", which apply different rules for determining whether a point is inside or outside a shape. These rules generate different effects only when a shape has intersecting lines or "holes" in it. Details are in "Filling Polygons That Have Intersecting Lines" earlier in this chapter.

## Stroke

**stroke**
The stroke color, as described in "Specifying Colors."

**stroke-width**
Width of stroke; may be given as user coordinates or with a length specifier. The stroke width is centered along the abstract grid lines.

**stroke-opacity**
A number ranging from 0.0 to 1.0; 0.0 is entirely transparent, 1.0 is entirely opaque.

**stroke-dasharray**
A series of numbers that tell the length of dashes and gaps with which a line is to be drawn. These numbers are in user coordinates only.

**stroke-linecap**
Shape of the ends of a line; has one of the values "butt" (the default), "round", or "square".

**stroke-linejoin**
The shape of the corners of a polygon or series of lines; has one of the values "miter" (pointed; the default), "round", or "bevel" (flat).

**stroke-miterlimit**
Maximum ratio of length of the miter point to the width of the lines being drawn; the default value is 4.


## Units:

**em**
The font size of the default font, usually equivalent to the height of a character

**ex**
The height of the letter x px
Pixels
**pt**
Points (1/72 of an inch)
**pc**
Picas (1/6 of an inch)

**cm**
Centimeters

**mm**
Millimeters

**in**
Inches

<svg width="200" height="150">
<svg width="200px" height="200px">

Both of these specify an area 200 pixels wide and 150 pixels tall.
