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