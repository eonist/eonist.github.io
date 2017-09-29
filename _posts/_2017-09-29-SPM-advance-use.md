My notes on advance usage of SPM <!--more--> 

### Update an SPM project:

The command swift package generate-xcodeproj does not modify your source code, so you can safely run the command after updating your dependencies.

To update your dependencies in your project, follow these three steps:

Update the dependency versions in your Package.swift file
1. Run swift package update
2. Run swift package generate-xcodeproj
3. In these steps Swift Package Manager will download the updated dependencies and then update your xcodeproj files to use those dependencies.'. 



### Use-full SPM commands:

`swift package init --type library` This will create the directory structure needed for a library package


### The SPM Ship Manifest 🛳:

```swift
// swift-tools-version:4.0
// The swift-tools-version declares the minimum version of Swift required to build this package.

import PackageDescription

let package = Package(
    name: "TestApp2",
    products: [
        // Products define the executables and libraries produced by a package, and make them visible to other packages.
        .library(
            name: "TestApp2",
            targets: ["TestApp2"]),
    ],
    dependencies: [
        // Dependencies declare other packages that this package depends on.
        // .package(url: /* package url */, from: "1.0.0"),
    ],
    targets: [
        // Targets are the basic building blocks of a package. A target can define a module or a test suite.
        // Targets can depend on other targets in this package, and on products in packages which this package depends on.
        .target(
            name: "TestApp2",
            dependencies: []),
        .testTarget(
            name: "TestApp2Tests",
            dependencies: ["TestApp2"]),
    ]
)

```