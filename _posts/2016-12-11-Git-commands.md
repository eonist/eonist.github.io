Overview of basic git commands<!--more--> 

**Content of page:**
1.  [Add](#add)
2.  [Log](#log)
3.  [Name and email](#set-your-name-and-email) 
4.  [Status](#status) 
5.  [Commit](#commit) 
5.  [Push](#push)
6.  [Pull](#pull) 
7.  [Attach remote repo](#attach-remote-repo) 
8.  [Cherry](#cherry) 
9.  [Manual clone](#manual-clone) 
10. [Clone into folder](#clone-into-folder) 
11. [Go into the a folder](#go-into-the-a-folder) 
12. [Using nano](#using-nano) 
13. [Escaping a terminal task](#escaping-a-terminal-task)

### **Add** 
Adds files to a commit
``git add *``
**Caution:** when you delete a local file, don't use: ``git add *``, you must specify the exact file  

### **Log**  
Get the latest log of what has changed, less verbose with pretty oneline  
``git log --pretty=oneline``

### **Set your name and email**  
```bash
git config --global user.email you@example.com  
git config --globaluser.name “username”
```  

### **Status** 
get the current status 
``git status``

### **Commit**  
Current changes w/ msg
``git commit -m "created index file"  ``

### **Push** 
Uploads the current from the local git commits to the remote git     
``git push origin master  ``  
**Caution:** you may want to do a "git fetch origin master" after a push, to arrive at the same point in time as the remote, as an alternative you can just push again and you will arrive at the the same point in time as the remote.

### **Pull**  
Download remote commits to local repo   

```bash
git pull origin master
/usr/local/git/bin/git push https://user:pass@github.com/user/test.git master
```
In the simplest terms, "git pull" does a git fetch followed by a git merge.


### **Attach remote repo**:   
Attach a remote location to the local git
``git remote add origin https://github.com/user/test.git``  

```bash
git remote rm origin    
git remote add origin 'git@github.com:username/repo.git' 
```   
Maybe:  

```bash
git remote set-url origin git@github.com:user/test.git  
git remote set-url origin https://user:pass@github.com/user/test.git  
```
### **Cherry:** 
Can be used to assert if there are any local commits ready to be pushed  
``git cherry -v https://user:password@github.com/user/test.git master `` 

### **Manual clone** 
supports pre existing files in a folder  

```bash
"git init" <--Installs the invisible .git folder   
"git remote add origin https://github.com/eonist/testing.git" <-- attach a remote repo  
"git fetch origin master" <--Download the latest .git data  
"git checkout master" <-- Downloads all the **files** from the remote repo  
"git fetch origin master" <-- Again to download the latest .git data  
```

### **Clone into folder:**  
**Caution:** Folder must be empty, Can't have preexisting files  
``git clone https://github.com/eonist/testing.git /Users/John/test``

### **Go into the a folder** 
The tilde char represents the user folder:  (use ../to get backward)  
``cd ~/Desktop/test``

### Escaping a terminal task
``ctrl + z``

### Using nano
Use nano as git text editor
``git config --global core.editor "nano"  ``

**Very good:** https://www.atlassian.com/git/tutorials And also the git pocket ref book 