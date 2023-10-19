Add assets to SPM<!--more--> Only for Swift tools version of 5.3 or later

### Examples
In the Package.swift
```swift
targets: [
    .target(
        name: "MyLibrary", //  consider using a subfolder for resources to distinguish them from source files,  For example, put all resource files into a directory named Resources, resulting in all of your resource files residing at Sources/MyLibrary/Resources.
        resources: [
            .process("text.txt")]
    ),
]
```
In the code:
```swift
let settingsURL = Bundle.module.url(forResource: "settings", withExtension: "plist")
```

### Gotchas:
- You can set folder paths and file paths as resources

### Update for spm 5.6+:

- `Resources/Assets.bundle`

Package file:
```swift
// swift-tools-version: 5.6
import PackageDescription
let package = Package(
    name: "AssetLib",
    products: [
        .library(
            name: "AssetLib",
            targets: ["AssetLib"]),
    ],
    dependencies: [],
    targets: [
        .target(
            name: "AssetLib",
            dependencies: [],
            resources: [.copy("Resources/Assets.bundle")] // .copy is key to retaining directory structure
         )
    ]
)
```

SPM file:
```swift
import Foundation

public struct AssetLib {
   public static let bundle: Foundation.Bundle = Foundation.Bundle.module // important for accessing the resource from external packages etc
}
```

Using the assetlib in another package or project:
```swift
import FileSugar

guard let resourcePath = AssetLib.bundle.resourcePath else { Swift.print("no resource path"); fatalError() }
let assetsPath = resourcePath + "/Assets.bundle/"
let filePath1 = assetsPath + "test.txt"
let fileContent = FileParser.content(filePath: filePath1)
Swift.print("fileContent:  \(String(describing: fileContent))")
let filePath2 = assetBundlePath + "/docs/" + "testing.txt" // Access subfolders
```

### Resources
- General SPM assets: [https://developer.apple.com/documentation/swift_packages/bundling_resources_with_a_swift_package](https://developer.apple.com/documentation/swift_packages/bundling_resources_with_a_swift_package)
- Localizing spm assets: [https://developer.apple.com/documentation/swift_packages/localizing_package_resources](https://developer.apple.com/documentation/swift_packages/localizing_package_resources)
- Binary distro: [https://developer.apple.com/documentation/swift_packages/distributing_binary_frameworks_as_swift_packages](https://developer.apple.com/documentation/swift_packages/distributing_binary_frameworks_as_swift_packages)
- Local spm modules: [https://developer.apple.com/documentation/swift_packages/developing_a_swift_package_in_tandem_with_an_app](https://developer.apple.com/documentation/swift_packages/developing_a_swift_package_in_tandem_with_an_app)
- Bundle.module for mac: [https://developer.apple.com/forums/thread/650158](https://developer.apple.com/forums/thread/650158)
- Unit testing with SPM resources: [https://stackoverflow.com/a/61263653/5389500](https://stackoverflow.com/a/61263653/5389500)