Overview of basic git concepts<!--more-->

**Content of page:**
1.  [Staging](#staging)
2.  [Committing](#committing)
3.  [Merging](#merging)
4.  [Resetting](#resetting)
5.  [Reverting](#reverting)
5.  [Checking out](#checking-out)
6.  [Logging](#logging)
7.  [Rebasing](#rebasing)
8.  [Status](#status)
9.  [Workflows](#workflows)
10. [Resolving conflicts](#resolving-conflicts)
11. [Clean](#clean)

### **Staging**
You "stage" files you want to commit The reason staging exists is because sometimes you don't want to commit all file changes. But only a few.

### **Committing**
You commit the file changes you have "staged"

### **Merging**
You merge file changes from (remote to local) or from (local to remote).

### **Resetting**   
git reset is designed to undo local changes.
The git reset HEAD~2 command moves the current branch backward by two commits  

### **Reverting**  
Generate a new commit that undoes all of the changes introduced in <commit>, then apply it to the current branch. reverting is designed to safely undo a public commit,

### **Checking out**  
Checkout is a way to move back and forward in your code history.  
The git checkout command serves three distinct functions: checking out files, checking out commits, and checking out branches.

### Logging
Get a list of the latest changes in the repository. Can be used to browse the history of all commits etc.

### **Rebasing**
Use interactive rebase to remove or squash unnecessary commits

### **Status**
Lets you know if there are any changes staged and ready to be committed. Or if you are behind or in front of the remote repository.

### **Workflows**
There are many ways to use git. Some like to only work on branches and then merge branches to a master once the update to a project feels solid. Others like to work directly on the master branch. Etc etc.

### **Resolving conflicts**
In its most basic form you can either have a newer file change on your local machine or on a remote machine.

### **Clean**
- The git clean command removes untracked files from your working directory. This is really more of a convenience command, since it’s trivial to see which files are untracked with git status and remove them manually. Like an ordinary rm command, git clean is not undoable, so make sure you really want to delete the untracked files before you run it.
- The git clean command is often executed in conjunction with git reset --hard. Remember that resetting only affects tracked files, so a separate command is required for cleaning up untracked ones. Combined, these two commands let you return the working directory to the exact state of a particular commit.
