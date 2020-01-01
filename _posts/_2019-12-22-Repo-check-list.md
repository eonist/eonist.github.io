My check-list when creating a Swift module / framework / project<!--more-->

- Create repo on github.com
- Download repo via github desktop
- Create SPM package `terminal: swift package init` [SPM gotchas](http://eon.codes/blog/2019/11/26/SPM-gotchas/)
- Create XCode project
- Add [Swift-lint](http://eon.codes/blog/2019/04/23/Swift-lint/)
- Add [Unit test](http://eon.codes/blog/2018/10/11/unit-test/)
- Check if tests pass locally: `terminal: swift test`
- Add CI tests: [GitHub Actions](http://eon.codes/blog/2019/12/13/spm-and-github-action/)
- Add a cronjob schedule for the CITest via crontab.guru
- Add instructions on how to use the framework and CI badges in the repo
- Add [CodeBeat](https://www.CodeBeat.co) code healthy badge to the repo
