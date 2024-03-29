My notes on parsing complex json in swift<!--more-->

```json
{
	"repoDetailView":[
		"TextInput": { "id": "name", "text": "Name: " },
		"TextInput": { "id": "local", "text": "Local-path: " },
		"TextInput": { "id": "remote", "text": "Remote-path: " },
		"TextInput": { "id": "branch", "text": "Branch: " },
	]
}
```

### decodeIfPresent:

```swift
struct Person: Mappable {
  let birth: Date
  let position: Coordinate
  let name: String?
  enum CodingKeys: String, CodingKey {
    case birth = "birth_date"
    // Response will contain lat & long but
    // we will store as coordinate
    case lat  
    case long
    case name
  }
  init(from decoder: Decoder) throws {
    let values = try decoder.container(keyedBy: CodingKeys.self)
    let birthString = try values.decode(String.self, forKey: .birth)
    let formatter = DateFormatter()
    formatter.dateFormat = "MM-dd-yyyy"
    birth = formatter.date(from: birthString)!
    // Parse lat and long to initialize coordinate
    let lat = try values.decode(Float.self, forKey: .lat)
    let long = try values.decode(Float.self, forKey: .long)
    position = Coordinate(lat: lat, long: long)
    name = try values.decodeIfPresent(String.self, forKey: .name)
  }
}
//usage:
let jsonString = """
{
\"birth_date\": \"11/21/1982\",
\"lat\": 29.321,
\"long\": 36.119
}
"""
let person = Person(jsonString: jsonString)
person!.position.lat // 29.321
type(of: person!.birth) // Date.Type
person!.name // nil
```


```swift
{
      "system":{"name":".SFUIText","size":18},
      "systemBold":{"name":".SFUIText-Semibold","size":18}
   }
```


### Nested struct decoding with decodable:

```swift
let json = """
        {
            "name": "The Whale",
            "whaleType": "Blue Whale",
            "whaleInfo": {
                "size":"22",
                "color":"blue"
            }
        }
        """.data(using: .utf8)! // our data in native (JSON) format

        //Example:
        guard let whale:Whale = try? decode(data: json) else {fatalError("unable to decode whale")}//From json to struct {
        Swift.print("whale.name:  \(whale.name)")
        Swift.print("whale.whaleType:  \(whale.whaleType)")
        Swift.print("whale.whaleInfo.size:  \(whale.whaleInfo.size)")
        Swift.print("whale.whaleInfo.color:  \(whale.whaleInfo.color)")


        struct Whale:Decodable{
    struct WhaleInfo:Decodable {
        let size:String
        let color:String
    }
    let name:String
    let whaleType:String
    let whaleInfo:WhaleInfo
}

```


### Some basic JSON:

```swift
import Foundation

var str = "Hello, playground"

let data = """
    {
        "fruit" : ["apple","orange"],
        "furnature" : ["bench","chair"]
    }
    """.data(using: .utf8)!

let firstKey: String = (try! JSONSerialization.jsonObject( with: data, options: .mutableContainers) as! [String: Any]).first!.key
Swift.print("firstKey:  \(firstKey)") // fruit
let keys: [String] = [String] ((try! JSONSerialization.jsonObject(  with: data,  options: .mutableContainers ) as! [String: Any]).keys)
Swift.print("keys:  \(keys)") //  ["fruit", "furnature"]
```
