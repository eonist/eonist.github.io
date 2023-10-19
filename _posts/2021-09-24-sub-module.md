Notes on submodules<!--more--> A great way to split up a project into smaller modules

### Resources:

[https://gist.github.com/gitaarik/8735255](https://gist.github.com/gitaarik/8735255) 

- Adding a submodule: (git-command url dir)
- Set terminal to the root of the project
```
git submodule add https://github.com/eonist/With.git Packages/External/With/
```

### Remove submodule
1. `git submodule deinit <relative-path-to-sub-module>`
2. `git rm <relative-path-to-sub-module>`

### Resetting submodule
Sometimes editing or changing a submodule just creates an avalanche of issues. So resetting can be a better option

1. Compress your project, to back it up (This can be smart to do even when adding a submodule etc, submodule is DarkMagic(TM) )
2. `git reset --hard`
3. Add submodules ✨

### Editing existing
1. Delete the folder with the submodule
2. Delete ref in .submodules and Delete ref in .git/.config (⚠️️ This part can sometimes be skipped ⚠️️)
3. force add with: `git submodule add --force https://github.com/eonist/With.git Packages/Remote/With/`

### Gotchas:
- Updating submodules: `git submodule update --remote` (will download updates from remote)
- The app github-desktop now has better support for updating submodules from the root project
- You can also inspect the gitsubmodule list by opening `.gitsubmodules` in the root dir of your project
