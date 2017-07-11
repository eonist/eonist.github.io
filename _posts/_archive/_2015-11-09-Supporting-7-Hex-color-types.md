There are 7 hex color formats: ""#FF0000","0xFF0000", "FF0000", "F00", "red", 0x00FF00 , 16711935
```swift
	NSColorParser.nsColor("#FF0000",1)//red nsColor
	NSColorParser.nsColor("FF0",1)//red nsColor
	NSColorParser.nsColor("0xFF0000",1)//red nsColor
	NSColorParser.nsColor("#FF0000",1)//red nsColor
	NSColorParser.nsColor("FF0000",1)//red nsColor
	NSColorParser.nsColor(0xFF0000,1)//red nsColor
	NSColorParser.nsColor(16711935,1)//red nsColor
```
<img width="283" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2015-11-10 at 10.38.27.png">

[The Code is here](https://github.com/eonist/swift-utils/blob/2882002682c4d2a3dc7cb3045c45f66ed59d566d/geom/color/NSColorParser.swift) 
