Notes on git cleaning<!--more-->

This can be a good idea for packages that needs to load fast. And where the history of commits doesnt matter or can be reset to init because the initial development isn't important to preserve.

### A 11 MB repo went to 10mb with these commands:
There will be nothing to commit 🤔
```
git gc --aggressive --prune=now
```

### Squashing could be a solution
- But won't work if there are merge commits

### Reset history:
- Copy and paste the bellow to reset repo to 1 init commit with all current files (Make a backup first, this series of commands are non recovarable)
```
git checkout --orphan newBranch
git add -A
git commit -m "Init"
git branch -D master
git branch -m master
git push -f origin master
git gc --aggressive --prune=all
```
- Ref: [https://gist.github.com/stephenhardy/5470814](https://gist.github.com/stephenhardy/5470814)
- bash script that does the same: [https://gist.github.com/Zibri/76614988478a076bbe105545a16ee743](https://gist.github.com/Zibri/76614988478a076bbe105545a16ee743)

### Alternative:
```
git init
git add .
git commit -m 'Initial commit'
git remote add origin [repo_address]
git push --mirror --force
```
