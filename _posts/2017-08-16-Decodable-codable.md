My notes on Decodable and Encodable in swift<!--more--> 

### Why Use Decodable / Encodable?

#### Pros:

- Decodable & Encodable are standard, so more people will know how to use them
- has some nice enum trickery (nested enum keys etc)

#### Cons:

- You could achive all of what these new features offer with reflection. 

- Must implement another required init for all subclasses 

### Examples:
- Nice simple example: https://stackoverflow.com/questions/44423309/swift-4-codable-decoding-json

- CodableKeys: (aka custom keys if json keys differ) http://ashishkakkad.com/2017/06/use-codable-coding-key-jsonencoder-jsondecoder-swift-4/

- Decode inheritance: https://stackoverflow.com/questions/44553934/using-decodable-in-swift-4-with-inheritance

- Nice official apple article: https://developer.apple.com/documentation/foundation/archives_and_serialization/encoding_and_decoding_custom_types

- init(decoder) ; http://www.yudiz.com/encoding-and-decoding-custom-types-with-swift-4/

- Decode and Encode extensions: https://github.com/chaione/Swift-4-JSON-Extensions/blob/master/Swift4JSON.playground/Pages/Read%20JSON%20with%20Extensions.xcplaygroundpage/Contents.swift

## Extensions:

```swift
struct Whale:Decodable,Encodable{
	let name:String
	let whaleType:String
}
let json = """
{
 "name": "The Whale",
 "whaleType": "Blue Whale"
}
""".data(using: .utf8)! // our data in native (JSON) format

//Global generic decode method for Decodable
func decode<T:Decodable>(data: Data) throws -> T {
	let decoder = JSONDecoder()
	return try decoder.decode(T.self, from: data)
}

//Encodable Extension
extension Encodable {
    func encode() throws -> Data {
        let encoder = JSONEncoder()
        encoder.outputFormatting = .prettyPrinted
        return try encoder.encode(self)
    }
}
//Example:
let whale:Whale = try decode(data: json)//From json to struct
let whaleJSON = try whale.encode()//From struct to json

```

### Resources:

Real KVC: https://github.com/bradhilton/SwiftKVC

Tut with playground: nested JSON structures
https://medium.com/makingtuenti/writing-a-scalable-api-client-in-swift-4-b3c6f7f3f3fb

https://medium.com/swiftly-swift/swift-4-decodable-beyond-the-basics-990cc48b7375