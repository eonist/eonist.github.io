My notes on porting objc to swift<!--more-->

### Import swift in objc classes:

1. In objc before porting to swift: `#import "WMPTrackListModule.h"` and after: `#import "WMPTrackListModule-Swift.h"`
2. prefix swift class: `@objc class SomeClass {}` with `@objc`


### Convert objc enum to swift enum:

```swift
@objc enum Bear: Int {
    case Black, Grizzly, Polar
}
```

```objc
Bear type = BearBlack;
switch (type) {
    case BearBlack:
    case BearGrizzly:
    case BearPolar:
       [self runLikeHell];
}
```
