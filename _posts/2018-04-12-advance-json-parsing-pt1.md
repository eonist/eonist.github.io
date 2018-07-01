My notes on adding Codable support for classes such as UIColor and UIFont which doesn't support Codable by default<!--more-->. This example only touches on Decodable, but the same thing can be done to make the class Encodable. 

### Theme.json 

```json
{
   "color":"white",
    "font":{"name":".SFUIText","size":"16"}
}
```

### Theme.swift

```swift
/**
 * A simple struct that holds UIColor and UIFont
 */
struct Theme:Decodable{
    enum CodingKeys: String, CodingKey {/*CodingKeys are required when you want to customize your json parsing*/
        case color
        case font
    }
    let color:UIColor
    let font:UIFont
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        color = try container.decode(key: .color, transformer: ColorTransformer())
        font = try container.decode(key: .font, transformer: UIFontTransformer())
    }
}
```

### DecodingTypes.swift

```swift
/**
 * The transformer that handles parsing the string value to UIColor
 * TODO: ⚠️️ Add support for 0x00FF00FF (Aka hex with alpha value)
 * EXAMPLE: "color":"white"
 */
class ColorTransformer: DecodingContainerTransformer {
    enum Error: Swift.Error { case cannotCreateColor(hex: String) }
    func decode(input: String) throws -> UIColor {
        guard let color = try? ColorUtils.color(input) else {
            throw Error.cannotCreateColor(hex: input)
        }
        return color
    }
}
/**
 * The transformer that handles parsing the dictionary (Aka fontName and fontSize) value to UIColor
 * EXAMPLE: "font":{"name":".SFUIText","size":"16"}
 */
class UIFontTransformer: DecodingContainerTransformer {
    enum Error: Swift.Error {  case cannotCreateFont(name: [String:String]) }
    func decode(input:[String:String]) throws -> UIFont {
        guard let name = input["name"], let fontSize = input["size"], let fontSizeAsDouble = Double(fontSize), let font = UIFont.init(name: name, size: CGFloat(fontSizeAsDouble)) else {
            throw Error.cannotCreateFont(name: input)
        }
        return font
    }
}
```

### DecodingHelpers.swift

```swift
/**
 * Makes the code more reusable/modular
 */
public protocol DecodingContainerTransformer {
    associatedtype DecodingInput
    associatedtype DecodingOutput
    func decode(input: DecodingInput) throws -> DecodingOutput
}
/**
 * Makes the code more reusable/modular
 */
extension KeyedDecodingContainer {
    public func decode<Transformer: DecodingContainerTransformer>(key: Key, transformer: Transformer) throws -> Transformer.DecodingOutput where Transformer.DecodingInput: Decodable {
        return try transformer.decode(input: try decode(Transformer.DecodingInput.self, forKey: key))
    }
    public func decodeIfPresent<Transformer: DecodingContainerTransformer>(key: Key, transformer: Transformer) throws -> Transformer.DecodingOutput? where Transformer.DecodingInput: Decodable {
        return try decodeIfPresent(Transformer.DecodingInput.self, forKey: key).map(transformer.decode)
    }
}

```

### AppDelegate.swift 

```swift
let urlStr:String = Bundle.main.resourcePath!+"/theme.json"
guard let data:Data = FileParser.data(urlStr) else {fatalError("wrong file path")}
guard let theme:Theme = try? decode(data: data) else {fatalError("can't be converted json to Theme")}
Swift.print("theme.color:  \(theme.color)")//UIExtendedSRGBColorSpace 1 1 1 1 
Swift.print("theme.font:  \(theme.font)")//<UICTFont: 0x7faef7c02850> font-family: ".SFUIText"; font-weight: normal; font-style: normal; font-size: 16.00pt
```

### Conclusion

By using This technique you can make any class Decodable with out resorting to wrappers or shadow DataContainers etc. 
Thanks to the swift-lang slack community for the help coming up with this solution. The supporting classes in this article can be found here [https://github.com/eonist/swift-utils](https://github.com/eonist/swift-utils)