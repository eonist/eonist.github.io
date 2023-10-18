My notes on app versioning <!--more-->

> If your app has never been versioned before, start with 1.0.0.

### Sem ver:
- Semantic Versioning Standard recommends 0.1.0
- Apple starts every project on 1.0.0
- First stable version going to be released on market should set version to 1.0

### Major minor patch:
- patch is for minor changes or bug fixes
- minor is for new functionality
- major is for major changes, like a rewrite of the project

### Legal examples:

1
1.1
1.1.1
1.10000
1.10000.1

### On the apple appstore :
- You have a version nr `1.0.0`
- and a build number: 1,2,3,4,etc. Grows by 1
- The version number and the build number values work together to uniquely identify the build and release for a particular App Store submission.
- For each new version of your app, you will provide a new unique version number and you may provide one or more builds (or submissions) each with a different and unique build number together with that same version number. All version numbers used in an app must be unique.
- For every new build you submit, you will need to invent a new build number (xcode-cloud does this automatically)
- For mac apps. you have to keep incrementing build nr. even if you change version nr. Does not apply for iOS.  For macOS apps, build numbers must monotonically increase even across different versions. In other words, for macOS apps you cannot use the same build numbers again in different release trains. iOS apps have no such restriction and you can re-use the same build numbers again in different release trains. (xcode-cloud does this automatically)
- ⭐ It is normal to use the same version number many times over and over again with different Build Numbers when uploading submissions for a particular release of your app.

### Based on complexity:
- For simple command line tools, I usually start around 0.9[.0] since I only consider releasing or packing them when they're near completion (or ready for beta testing, anyway.)
- More complicated projects start around 0.1[.0] and some never even see 1.0.
- I consider 1.0 a release version (or at least a locally tested beta or release candidate) and plan accordingly.

### Based on progress:
- First usable ready but not feature complete version, I normally try to judge how far along it is towards a feature complete version,
- So for example if my first usable is 33% feature complete I make the version number 0.3.0 or similar.
- Then as I move towards feature complete corresponding versions get given numbers in a similar way.

### Resources:
- Apple on versioning: https://developer.apple.com/library/archive/technotes/tn2420/_index.html
- Release version tags in git: https://git-scm.com/book/en/v2/Git-Basics-Tagging
- Random release name generator: https://www.npmjs.com/package/project-name-generator
- I depth discussion on apple versioning: https://stackoverflow.com/questions/21125159/which-ios-app-version-build-numbers-must-be-incremented-upon-app-store-release?rq=2

### Conclusion:
- For apple apps build number can be increased for new versions.
- So beta can be 1.0.0 and when it's releasable just push the latest build could be build nr. 23, 44, 120 etc.
- Then after it's released. Bump new versions as 1.0.1, 1.0.2. and for major updates. 1.1.0 etc
- It could be an idea to bump versions in testflight. CoreData schema might need to be updated etc.

### Gotchas:
- Some increment based on major, minor, patch principles. However this isn't a particullary user centeric design
- User facing incrementing can be: 1.0.9 -> 1.1.0 where each bump represents a week of work for instance. Skipping a version if two weeks go by etc.
