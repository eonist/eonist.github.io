Overview of basic Git workflows<!--more--> 

**Content of page:**
1.  [Git status](#git-status)
2.  [Merge local to remote](#merge-local-to-remote)
3.  [Manually clone into a populated folder: ](#to-manually-clone-into-a-populated-folder)
3.  [Use git as a local file merge tool](#use-git-as-a local-file-merge-tool)  

### Git status:

```bash
cd ~/Documents/your-project/
git status
```

### Merge local to remote:

1. make a change in remote repo to file text.txt
2. make a change in local repo to file text.txt
3. add and commit your changes locally
4. **"git fetch origin master"**
5. OPTIONAL: **"git log --oneline master..origin/master"** (this shows the commits that the remote is ahead)
6. **"git merge master origin/master"** (if there are conflicts the conflicting files will get text from both conflicting files)
7. Keep all local file changes: **"git checkout --ours *"** (you may replace the * char with a file or path)
8. Keep all remote file changes: **"git checkout --theirs *"** (you may replace the * char with a file or path)
9. Then add, commit and push
10. The commit history will now display remote commit, local commit, merge commit. 


### To manually clone into a populated folder 
1. git init
2. add and commit your files
3. attach remote origin  

Then do the exact same steps as you do in the "Merge local to remote workflow"


### **Use git as a local file merge tool** 
Basically you are doing this: repoA -> repoX (remote) ->repoB    
Pretext: I have a huge code lib that i recently updated alot of comments in from my ipad. Pre GitSync era. Now i want to merge back together with the lib on my macbok.  I can create a private repo on bitbucket, but i dont quite trust bitbucket with this code lib. So why not do it locally? We are using Github desktop app for doing the merging because it has a pretty good visual code diff feature. We use terminal to setup the "faux remote git"

1. You create 1 "bare repo" named "repoX" in terminal:
```bash
mkdir ~/Desktop/del/repo2.git
cd ~/Desktop/del/repo2.git
git --bare init
```
2. then you create 2 repos from the github desktop app named: "repoA" and "repoB" (use the add button)
3. you then click the repo properties for both repoA and repoB and set their remote to the path of repoX: "~/Desktop/del/repo2.git"
4. Add a file in repoA and publish it with the github desktop app. 
5. in repoB click sync in the github desktop app. and you will now get the file originally from repoA
6. Now the great thing about this is that you can add many files to repoA and then commit and sync from GitHub Desktop App and then in repoB you will get a nice visual feedback about the textual changes additions in green and subtractions of text as red. 
7. To avoid messing with checking out previouse commits if you dont like a change etc. I recomend just keeping a stash of the new and old code lib and then just copy paste in any file you didnt like. Basically copy the old one or the new one, which ever you liked the most. Very non-destructive. Merging rebasing, checking out etc is not easy and can very quickly go astray. And its cognativly tasking, when you should focus on getting the right changes etc. 

**Note:** Actually you may only need RepoA and then a RepoB that you set as remote. when you feed in old code files into repoA and then commit to RepoB, and then just replace these files in repoA with the "new " files, you will get a vidsual feedback of what has changed. This simplifyies things. the approch described above is still valid. just replace repoB with repoX and you are good. 

