A few workflows concerning Carthage and nested framework<!--more-->

⚠️ **PRE-REQUISITES:** ⚠️️ In your github repo you need to include .xcodeproj file with framework settings all setup. The settings should also include:   

1. Scheme is shared.   
<img width="473" alt="img" src="https://rawgit.com/stylekit/img/master/shared.gif">
2. testability set to YES under build settings. (Important if you want to use `@testable import`)  
3. Build settings -> Framework search paths set to: `$(SRCROOT)/Carthage/Build` (Carthage generates aliases in this folder) aka magic portals to other frameworks. (I think this is imp when children use libs higher up the graph, etc, more research needed) 🤔
4. `.gitignore` that excludes .framework files and other binaries (We should never have bins in repos)


## 1. The Automated workflow: ✅
1. Create a repo on github named "A" (this will be the child)
<img width="583" alt="img" src="https://rawgit.com/stylekit/img/master/frameworkproject.gif">
2. Create a repo on github named "B" (this will be the parent to the child)
3. In repo "B" add a Cartfile with `github "eonist/A" "master"` 👈 Informs carthage to build this before anything else
4. On your local machine create a Cartfile and add `github "eonist/B" "master"`
5. In terminal `carthage update` Now everything will download and be built in the right order. Children first 🔑
6. Drag and drop the .frameworks located in `Carthage/Build/<Platform>/`

## 2. The Manual workflow: 🚫
1. include all the repos you want to use in a cartfile on your local machine
2. In terminal Do ``carthage update --no-build`` (this downloads all repos but skips building)
3. Then do carthage build <name-of-repo> for each child dependency starting with the farthest descendants
4. When you have built your last item drag and drop the .frameworks located in ``Carthage/Build/<Platform>/``

## 3. Downloading prebuilt .framework files 🚫
If project owners include .framework files in their releases. (Some do, some don't) then using `carthage update --no-build` And the binaries will be placed in ``Carthage/Build/<Platform>/``

## 4. Using submodules: 🚫
Don't do it, Google it if your not familiar with why you shouldn't use submodules for nesting dependencies or just read this article [https://codingkilledthecat.wordpress.com/2012/04/28/why-your-company-shouldnt-use-git-submodules/](https://codingkilledthecat.wordpress.com/2012/04/28/why-your-company-shouldnt-use-git-submodules/) or  [this](https://www.cocoawithlove.com/blog/package-manager-fetch.html) That being said. Projects such as  [AlamoFireObjectMapper](https://github.com/tristanhimmelman/AlamofireObjectMapper) project do use submodules in conjunction with Carthage framework nesting.


## Final notes:
Which workflow is better? The future is automated so go with option 1. Also you need something that can work in a CI environment. The first workflow has a bit more bloat attached and carthage could have avoided this by allowing order take priority when building. (Feature request!!!) 👉 The optional workflows are written down in order to justify the extra contextual files one has to place in sub dependencies. 👈

## Further discussions:
Advance nesting does and don't's: [https://github.com/Carthage/Carthage/issues/768](https://github.com/Carthage/Carthage/issues/768)
