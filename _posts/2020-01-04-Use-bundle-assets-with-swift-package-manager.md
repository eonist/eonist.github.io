My notes on using bundle assets with SPM<!--more-->

### ResourceHelper:
By importing [ResourceHelper](https://github.com/eonist/ResourceHelper/) you can access resources in xCode unitTests as well as SPM Terminal/Github-actions unit-tests  

### Example code:
```swift
let path: String = ResourceHelper.projectRootURL(projectRef: #file, fileName: "temp.bundle/qrimg1.png").path
guard let uiImage = Image(contentsOfFile: path) else { Swift.print("err getting img"); return }
Swift.print("uiImage.size: \(uiImage.size)") // 500x500
```

### Resources:
[https://github.com/eonist/ResourceHelper/](https://github.com/eonist/ResourceHelper/)
