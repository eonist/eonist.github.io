Encoding and decoding of text<!--more-->, Use full when calling shell commands etc.


```swift
let str = "testing this stuff.121"

let escaped = str.stringByAddingPercentEncodingWithAllowedCharacters(NSCharacterSet.URLPathAllowedCharacterSet())
Swift.print("escaped: " + "\(escaped)")//escaped: Optional("testing%20this%20stuff.121")
//(test as NSString).UTF8String

let unEscaped = escaped!.stringByRemovingPercentEncoding
Swift.print("unEscaped: " + "\(unEscaped)")//unEscaped: Optional("testing this stuff.121")
```

```swift
let myFileString = "My File.txt"
if let urlwithPercentEscapes = myFileString.stringByAddingPercentEncodingWithAllowedCharacters( NSCharacterSet.URLPathAllowedCharacterSet()) {
    print(urlwithPercentEscapes)  "My%20File.txt"
}
```

## Options:

- URLFragmentAllowedCharacterSet()

- URLHostAllowedCharacterSet()

- URLPasswordAllowedCharacterSet()

- URLPathAllowedCharacterSet()

- URLQueryAllowedCharacterSet()

- URLUserAllowedCharacterSet()


```swift
let myUrlString = "http://www.mapquestapi.com/geocoding/v1/batch?key=YOUR_KEY_HERE&callback=renderBatch&location=Pottsville,PA&location=Red Lion&location=19036&location=1090 N Charlotte St, Lancaster, PA"

var urlSet = NSMutableCharacterSet()
urlSet.formUnionWithCharacterSet(.URLFragmentAllowedCharacterSet())
urlSet.formUnionWithCharacterSet(.URLHostAllowedCharacterSet())
urlSet.formUnionWithCharacterSet(.URLPasswordAllowedCharacterSet())
urlSet.formUnionWithCharacterSet(.URLQueryAllowedCharacterSet())
urlSet.formUnionWithCharacterSet(.URLUserAllowedCharacterSet())

if let urlwithPercentEscapes = myUrlString.stringByAddingPercentEncodingWithAllowedCharacters( urlSet) {
    print(urlwithPercentEscapes)  // "http://www.mapquestapi.com/geocoding/v1/batch?key=YOUR_KEY_HERE&callback=renderBatch&location=Pottsville,PA&location=Red%20Lion&location=19036&location=1090%20N%20Charlotte%20St,%20Lancaster,%20PA"
}
```


## Links:
http://stackoverflow.com/questions/8086584/objective-c-url-encoding

## Unicode guide:
https://www.objc.io/issues/9-strings/unicode/
