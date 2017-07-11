App brainstorming<!--more--> 

<img width="896" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-03-28 at 19.42.51.png">

## Feature brainstorming:

Activity (incoming/outgoing)
~~incoming~~ (all incoming) 
~~outgoing~~ (all outgoing)
Repos (list view)
Statistics (all repos)
Settings (app settings)

**UI**  
- Pull to sync  
- Search  
- Advance search  
- Add/remove repos  

### Activity (Incoming / Outgoing)
- Repo name, Repo date of commit, Commit title, Commit description - excerpt, branch
- contextual: branch="master" files-changed="6" sha1="4405aa474fff8247607d0bf599e054173da84113"

### Repo
The TopBar should have: add,remove,search,~~settings~~ 
- The search should be: "Filter repositories" 

**Repo list item:**  
- Repo name - branch
- Repo branch-name
- Repo URL

When you click a repo list item, you should be taken to a detail view of this item. Which slides in from the right.

### Repo-item-view:
repo-details, repo-commits, file-tree-list, commit-graph , commit-punchcard, add&del-graph, 

**Repo-Details-View**  
This view should have everything the repo list item has and full description. And quick options like:
- repo-name:String = "Element"
- repo-URL:String = "https://github.com/eonist/Element"
- local-path:String = "~/Desktop/test"
- branch-name:String = "master"
- Broadcast:Bool = true
- Subscribe:Bool = true
- Auto-sync:Bool = true
- Auto-sync-interval:Int = 30sec
- ~~delete this repo (cmd+del)~~ Delete via right click in repoList, or by swipe
- ~~Auto-sync-file-change:Bool~~ (not yet)

### Statistics (across all projects)
add&del-graph, commit-graph , commit-punchcard

### Settings
- global auto sync interval (30sec)
- global auto sync file change (Bool)
- Prompt for commit title and desc (Bool)
- Theme-picker: Dark, Light (Via a ComboBox)

- [x] Try to see if you can get `git status` to work
- [x] Assert if keychain integration works
- [x] Assert if the Auto-git-process works
- [x] setup the application with icons and views etc. (Drop the translucency at first)
- [x] create the repo list view first. adding and removing should work (store to xml)
- [ ] create the merge-conflict-resolution-dialog as a ListView
- [ ] create the repo details view. With a save button that saves to xml
- [ ] create the activity view. Each item should have commit title, commit desc, repo name, and date of commit
- [ ] Add translucency to the leftSideBar
- [ ] add the icons as RadioButton with text describing what they do