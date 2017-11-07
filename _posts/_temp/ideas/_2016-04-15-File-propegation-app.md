Case study of the File-Propagating-App<!--more--> The concept is simple. you have an xml list with origin and target folder paths. An applescript app will be running on a 60 sec interval calling the rsync command on each of the items in the xml list:

**Why build such an app?**  
I need an app that clones content of folder to other folders. Ideally it would do this only when there is an actual change. But apple's folder watching does not detect when files are deleted. So a 60sec interval is the next best thing. 

**Why do I need to clone content of folders to other folders?**   
To automate propagating file changes to projects that use the same dependencies. Great when you contribute to many projects on github etc. 

**Why build an applescript app?**  
MVP - Minimal Viable Product. I always try to use minimal effort to prove an app idea works. You can always polish and make something more elaborate later. With a fancy GUI etc. 
 
- 1. [Example](#example) 
- 2. [Notes](#notes) 
- 3. [Tasks](#tasks) 

## Example:

**Bash:**  
  
This line clones the content from origin folder to target folder: (one way clone)

```bash
rsync -va --delete ~/Desktop/test/ ~/Desktop/test2/
```

## Notes:


## Tasks:


- [ ] ==Write a XML format ==
- [ ] Try to call the bash command from applescript
- [ ] Setup the app to parse the xml and run the bash command on each item every 60 sec
- [ ] Create an app icon with the new App Icon system. 
- [ ] Figure out how to diff two folder hierarchies, as an assert before running rsync. Might not be need as rsync does this it self probably. 