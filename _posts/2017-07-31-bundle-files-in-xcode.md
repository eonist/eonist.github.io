My notes on bundling and reading files in an XCode project<!--more--> 

1. Create a test.txt file and add the text "testing✌️" to it then put it in a folder named test.bundle
2. Drag and drop it next to your .app file in xcode (copy)
3. `Swift.print(Bundle.main.resourcePath!+"/temp.bundle/test.txt")` Output: /Users/James/Library/Developer/Xcode/DerivedData/GitSyncMac-heiwpdjbtaxzhiclikjotucjguqu/Build/Products/Debug/GitSyncMacApp.app/Contents/Resources/temp.bundle/test.txt


```swift
Swift.print(content(Bundle.main.resourcePath!+"/temp.bundle/test.txt"))//Output: testing✌️
static func content(_ path:String)->String?{
    do {
        let content = try String(contentsOfFile:path, encoding:String.Encoding.utf8) as String//encoding: NSUTF8StringEncoding
        return content
    } catch {
        return nil
    }
}
```