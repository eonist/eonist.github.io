My notes on bundling and reading files in an XCode project<!--more-->

1. Create a test.txt file and add the text "testing✌️" to it then put it in a folder named `temp.bundle`
2. Drag and drop it next to your .app file in xcode (tick the copy if need checkbox)
3. Test if it works: `Swift.print(Bundle.main.resourcePath!+"/temp.bundle/test.txt")` Output: /Users/James/Library/Developer/Xcode/DerivedData/GitSyncMac-heiwpdjbtaxzhiclikjotucjguqu/Build/Products/Debug/GitSyncMacApp.app/Contents/Resources/temp.bundle/test.txt


```swift
static func content(_ path: String) -> String? {
    do {
        let content = try String(contentsOfFile: path, encoding: .utf8) as String//encoding: NSUTF8StringEncoding
        return content
    } catch {
        return nil
    }
}
Swift.print(content(Bundle.main.resourcePath! + "/temp.bundle/test.txt"))//Output: testing✌️

// or:

let filePath = Bundle.main.resourcePath! + "/temp.bundle/test.txt"
let content: String? = try? String(contentsOfFile: filePath, encoding: .utf8) as String // encoding: NSUTF8StringEncoding

```

### Why use .bundle folders?:
Dragging a folder with resources into xcode will also work but the content will not have hierarchical folder structure once you build. .bundle folders on the other hand will retain the hierarchical folder structure 	

### Note:
- If you update something in a .bundle the .bundle in your app also get's updated. It does not get updated other wise.
- Sometimes you have to add the .bundle file to app target -> buildphases -> copy bundle resources
- If you need to access a file quickly: `var sourceURL = URL(fileURLWithPath: Bundle.main.resourcePath!);sourceURL.appendPathComponent("archive.zip")` (drag the file into src of xcode)

### Alternativly:

```swift
//text
let file = NSString(string:"~/Desktop/HCCQR.txt").expandingTildeInPath
let p = URL(fileURLWithPath: file)
let text = try? String(contentsOf: p)
Swift.print("text:  \(text)")

//img:
let path = Bundle.main.resourcePath!+"/temp.bundle/HCCQR.png"
let uiImage:UIImage? = UIImage.init(contentsOfFile: path)
let uiimageview = UIImageView.init(image: uiImage)
view.addSubview(uiimageview)
```
