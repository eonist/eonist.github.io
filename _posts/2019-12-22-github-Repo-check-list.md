My check-list when creating a Swift module / framework / project<!--more-->

- Create repo on github.com
- Download repo via github desktop
- Create SPM package `terminal: swift package init` [SPM gotchas](http://eon.codes/blog/2019/11/26/SPM-gotchas/)
- Create XCode project
- Add [Swift-lint](http://eon.codes/blog/2019/04/23/Swift-lint/)
- Add [Unit test](http://eon.codes/blog/2018/10/11/unit-test/) (Optional)
- Check if tests pass locally: `terminal: swift test` or builds `terminal: swift build`
- Add CI tests: [GitHub Actions](http://eon.codes/blog/2019/12/13/spm-and-github-action/)
- Add a cronjob schedule for the CITest via [crontab.guru](crontab.guru) 
- Add instructions on how to use the framework and CI badges in the repo
- Add [CodeBeat](https://www.CodeBeat.co) code healthy badge to the repo

### Schedule cron builds on github:
The nice thing with cron jobs is if you have nested dependencies, the build badges higher up will start to go red if the sub-modules they relay on start to break down.

```yaml
on:
  push:
    branches:
    - master
  schedule:
  - cron: "0 18 * * 0-6"
```

### Github action badge syntax:
```
[![Github actions badge](https://badgen.net/github/checks/eonist/With?icon=github&label=Tests)](https://github.com/eonist/With/actions)
```
*Result:*   
[![Github actions badge](https://badgen.net/github/checks/eonist/With?icon=github&label=Tests)](https://github.com/eonist/With/actions)


### Compact checklist (easy to add as a todo-list etc)
And after this list you can start adding things you want the project to be.
```swift
- Make Github repo
- Make SPM Package
- Make XCode project
- Add linter
- Add unit-test
- Add Github-Actions
- Edit ReadMe.md
```

### Reach goals:
- Add Automated code-reviews based on AI
- Add automated UITests
- Add code coverage services like codacy codeclimate etc