These are my notes on prototyping with local Swift packages<!--more-->

### Why prototype with local packages?
- Allows for easy iteration on early versions of a package
- Faster compile times than remote packages
- Share packages across many Xcode projects (iOS / macOS)
- With local packages, Xcode will be more stable if you have a lot of nested frameworks that change frequently

### How to set up a local package structure for prototyping
- Create a folder called `/Packages/` in the root directory of your project.
- Create Swift Package Manager projects using the terminal by running the command `swift package init` in the `/Packages` folder.
- Drag the packages you need from the `/Packages/` folder into Xcode and ensure they compile correctly.
- Add the packages to your Xcode projects by dragging them into the project and adding them in the `General` tab by clicking the `+` button in the `Frameworks` section. (Note: this may not work in a workspace, so open the Xcode project by itself.)
- Create an Xcode workspace and drag the Xcode projects into the workspace.
- You can add remote packages to the local packages if needed.
- You can also create a `Common` package that serves as an umbrella package for efficient reuse.

### Final note:
- With this setup, you can structure your prototype projects with proper framework structures, making your code more modular, single-purpose, and easier to reason about.

### Gotchas:
- Embedding packages as submodules can be a great way to speed up Xcode build times.