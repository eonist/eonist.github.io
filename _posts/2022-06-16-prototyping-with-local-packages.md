My notes on prototyping with local swift packages<!--more-->

### Why prototype with local packages?
- Easy to iterate early versions of a package
- Faster compile times than remote packages
- Share packages across many xCode projects (iOS / macOS)
- With local packages XCode will be more stable if you have a lot of nested frameworks that change a lot etc

### How to setup a local package structure for prototyping
- Make a folder called `/Packages/`
- In the root folder add your mac and iOS projects
- Make swift package manager projects with terminal: `swift package init` in the `/Packages` folder
- Drag the packages you need from the `/packages/` folder into xCode and make sure they compile etc
- Drag the packages into xCode projects and also add them in `general tab` by clicking + in the `frameworks section` (sometimes this doesnt work in workspace, so open the xcode proj by it self)
- Create a xCode workspace and drag the xCode projects into the workspace
- You can add remote packages in the local packages if you need to add remote packages etc
- You can also create a `Common` packages which serves as sort of umbrella package for efficient reuse etc

### Final note:
- Now you can structure your prototype projects with proper framework structures
- Making code more modular and single purpose and easier to reason about
