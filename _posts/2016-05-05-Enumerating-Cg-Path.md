Notes on Parsing CGPath <!--more--> 

Reading CGPath instances

Enumerating UIBEzierCurve but it can be ported: http://oleb.net/blog/2012/12/accessing-pretty-printing-cgpath-elements/ and the updated version: http://oleb.net/blog/2015/06/c-callbacks-in-swift/


CGPAthApply seems to be the method to use it takes an array and a callback method. here is an obj-c implementation: http://www.mlsite.net/blog/?p=1312


Seems to have the answer in swift: http://stackoverflow.com/questions/24274913/equivalent-of-or-alternative-to-cgpathapply-in-swift

A wrapper: https://gist.github.com/zwaldowski/e6aa7f3f81303a688ad4

obj-c wrapper: https://gist.github.com/adamawolf/3823502

```swift
//These are the Element types: 
public enum CGPathElementType : Int32 {
    case MoveToPoint
    case AddLineToPoint
    case AddQuadCurveToPoint
    case AddCurveToPoint
    case CloseSubpath
}
```

See CGPathExtensions.swift here for code that can enumerate CGPath's in swift: https://github.com/eonist/swift-utils