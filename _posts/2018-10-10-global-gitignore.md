My notes on global gitignore <!--more-->
### In terminal: (macOS Mojave)

1. `touch ~/.gitignore_global`
2. `open .gitignore_global` add: `.DS_Store`
3. `git config --global core.excludesfile ~/.gitignore_global`


And you're done 🎉

### To remove .ds_store files from project:
- Commit all files except `.ds_store` files
- git stash all the `.ds_store` files
