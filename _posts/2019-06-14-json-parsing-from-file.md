My notes on parsing json from a local file<!--more-->

### Example:
```json

  {
      "regions" : [
          {"language":"English", "artist":"Beyonce", "album":"Lemonade"},
          {"language":"Portuguese", "artist":"Enrique Iglesias", "album":"Escape"},
          {"language":"Spanish", "artist":"Shakira", "album":"Magia"}
      ]
  }

```

```swift
import JSONSugar
import FileSugar

let urlStr = Bundle.main.resourcePath! + "/temp.bundle/featureArtist.json"
let url = URL(fileURLWithPath: urlStr)
guard let data:Data = FileParser.data(url:url) else {fatalError("wrong file path")}
guard let featuredData:FeaturedData = try? decode(data: data) else {fatalError("can't be converted json to Theme")}
featuredData.regions.forEach {
    Swift.print("language: \($0.language) artist: \($0.artist) album:  \($0.album)")// Lemonade, Escape, Magia
}
/**
 * Data structure
 * ## Examples:
 * let region1: Region = .init(language: English", artist:"Beyonce", album:"Lemonade"))
 * let region2: Region = .init(language: Spanish", artist:"Shakira", album:"Magia"))
 * let featured: Featured = .init(regions: [region1,region2])
 */
struct FeaturedData : Codable{
    struct Region : Codable {
        let language: String
        let artist: String
        let album: String
    }
    let regions: [Region]
}

```
