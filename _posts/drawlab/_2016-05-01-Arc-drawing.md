Arc drawing in OSX:<!--more--> 

- The **CGPathAddLineToPoint** method is great for roundedCorners in a rect where you know the converging point. But not so great when you dont know the convering point. You can calculate it by using the in angle of the startPoint of an arc and the in angle of the end point of an arc but then thats alot of calculations etc. 

- The CGPathAddArc method doesnt seem to support drawing from negative area to positive area. The CGPathAddRelativeArc method supports this