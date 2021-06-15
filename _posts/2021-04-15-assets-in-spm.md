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

### Resources
- General spm assets: https://developer.apple.com/documentation/swift_packages/bundling_resources_with_a_swift_package
- Localizing spm assets: https://developer.apple.com/documentation/swift_packages/localizing_package_resources
- Binary distro: https://developer.apple.com/documentation/swift_packages/distributing_binary_frameworks_as_swift_packages
- Local spm modules: https://developer.apple.com/documentation/swift_packages/developing_a_swift_package_in_tandem_with_an_app
