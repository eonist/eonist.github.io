These are my notes on app versioning.<!--more-->

### Sem ver:
- The Semantic Versioning Standard recommends starting with version 0.1.0.
- However, Apple starts every project on version 1.0.0, and the first stable version that's going to be released on the market should be set to version 1.0.

### Major minor patch:
- The patch version is for minor changes or bug fixes.
- The minor version is for new functionality.
- The major version is for major changes, like a rewrite of the project.

### Legal examples:

```
1
1.1
1.1.1
1.10000
1.10000.1
```

### On the Apple App Store:
- Each app on the Apple App Store has a version number and a build number. The version number is a unique identifier for each new version of your app, while the build number is a unique identifier for each submission of that same version.
- For each new build you submit, you must provide a new build number. Xcode Cloud can automatically generate build numbers for you.
- For macOS apps, build numbers must increase monotonically even across different versions. This means that you cannot reuse the same build numbers in different release trains. However, iOS apps have no such restriction, and you can reuse the same build numbers in different release trains. Xcode Cloud can automatically handle build numbers for you.
- It is common to reuse the same version number many times with different build numbers when uploading submissions for a particular release of your app.

### Based on complexity:
- For simple command line tools, I usually start the version number around 0.9[.0] since I only consider releasing or packaging them when they're near completion or ready for beta testing. 
- For more complicated projects, I start the version number around 0.1[.0], and some projects never even reach version 1.0. 
- I consider version 1.0 to be a release version or at least a locally tested beta or release candidate, and I plan accordingly.


### Based on progress:
When I'm working on a project, I like to base my version numbers on how far along the project is towards being feature complete. As I make progress towards feature completeness, I assign version numbers that reflect the level of completion. For example, if my first usable version is 33% feature complete, I might assign it the version number 0.3.0 or something similar. Then, as I move towards feature completeness, corresponding versions get assigned numbers in a similar way.

### Resources:
- Apple on versioning: https://developer.apple.com/library/archive/technotes/tn2420/_index.html
- Release version tags in git: https://git-scm.com/book/en/v2/Git-Basics-Tagging
- Random release name generator: https://www.npmjs.com/package/project-name-generator
- In-depth discussion on Apple versioning: https://stackoverflow.com/questions/21125159/which-ios-app-version-build-numbers-must-be-incremented-upon-app-store-release?rq=2

### Conclusion:
For Apple apps, the build number can be increased for new versions. For example, a beta version could be assigned the version number 1.0.0, and when it's ready for release, the latest build (e.g. build number 23, 44, 120, etc.) can be pushed. After release, new versions can be bumped up as 1.0.1, 1.0.2, and for major updates, 1.1.0, etc. It may also be a good idea to bump versions in TestFlight, as the Core Data schema might need to be updated.

### Gotchas:
Some developers increment version numbers based on major, minor, and patch principles. However, this approach may not be user-centric. A more user-friendly approach could be to increment version numbers in a way that reflects the amount of work completed. For example, going from version 1.0.9 to 1.1.0 could represent a week of work, and skipping a version could indicate that two weeks have gone by without a release.