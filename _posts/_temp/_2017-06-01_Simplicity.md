Getting a working prototype up and running is vital to the progress of any project. Preferably within 1 day of work. To achieve this I want to use JSON to abstract away the UI code. But at the same time not closing the door on  customization. Here is my Idea:

1. You write the app structure in JSON. 
2. Then you add some CSS that use FlexBox to position the layout. 
3. Then you add some event handling swift code that reacts to interaction

Test your app: Does it resize correctly, does the page transitions work?

#### SWIFT:
This is just 1 event Handler for the entire App to test it quickly. This isn't sustainable in the long run as you build out the app. You can extract the event Logic into other methods. Or subclass the Views. I.e: `RepoView` you then specify to use this class in the JSON. 
```swift

self.onEvent{ event in
	if event.type == .buttonClick && event.immediate == "commitPrompt" && event.origin.id == "ok" {
		/*Add commit logic here*/
		Nav.setView(.previous)
	}else if event.type == .select && event.immediate == "repo" {
		Nav.view = Nav.getView("repoDetail")
		let idx:[Int] = event.index
		if isGroup(idx,json) {
			 Nav.setView("repo")
			 /*populate list with new idx level*/
		}else {
			 Nav.setView("repoDetail")
		}
	}
	else if event.type == .swipeRight{Nav.back()}
	else if event.type == .swipeLeft{Nav.forward()}
	else if event.type == .rightDown && event.immediate.id == repo{
		NSMenu.popUpContextMenu(Logic.contextMenu["repo"], with: (event as! ButtonEvent).event!, for: self)
	}
}

```

#### CSS:
```css
Window{
	min-height:400;
	min-width:300;
	max-height:800;
	max-width:600;
}
#main{
	display:flex;
	flex-dir:row;/*Aligns the UI horizontally*/
	flex-grow:1;/*Fill the remainder hor space*/
}
#sideMenu{
	width:100px;
	display:flex;
	flex-dir:column;/*Aligns the UI vertically*/
	align-items:center;/*Centers the Elements in the column*/
}
#repoDetail,#prefs,#commitMsg,#mergePrompt,#errorPrompt{
	display:flex;
	flex-dir:collumn;
	width:100%;
	height:100%;
}
#confirm{/*OK,cancel button*/
	display:flex;
	flex-dir:row;
}
```


#### JSON:
```
{
	"title":"GitSync",
	"size":{w:400,h:600},
	"pages":{
		"View":{
			id:repo,
			content:[
				RepoList:{
					id:repoList,
					itemHeight:24
					url:"~/Desktop/repo.xml"
				}
			]
		},
		View:{
			id:repoDetail,
			content:{
				TextInput:{id:name,text:Name},
				TextInput:{id:local,text:Local-path},
				TextInput:{id:remote,text:Remote-path},
				TextInput:{id:branch,text:Branch},
				CheckBoxTextButton:{id:auto,text:Auto,checked:false}
			}
		},
		View:{
			id:"prefs",
			content:{
				TextInput:{id:login,text:"github-user"}
				TextInput:{id:pass,text:"github-password"}
				CheckBoxTextButton:{id:auto,text:Auto,checked:false}
			}
		}
		View:{
			id:errorPromt,
			content:[
				Text:{id:title,text:"There was an error"}
				Container:{
					class:"confirm",
					content:{
						TextButton:{id:ok},
						TextButton:{id:cancel}
					}
				}
			]
		}
		View:{
			id:commitPrompt,
			content:[
				Text:{id:title,text:"Commit changes"},
				TextArea:{id:msg},
				Container:{
					class:"confirm",
					content:{
						TextButton:{id:ok},
						TextButton:{id:cancel}
					}
				}
			]
		}
		View:{
			id:MergePrompt,
			content:[
				Text:{id:desc},
				RadioGroup:[
					RadioButtonButton:{id:local,title:"Use local file"},
					RadioButtonButton:{id:remote,title:"Use remote file"},
					RadioButtonButton:{id:remote,title:"Use mix of both"}
				],
				Container:{
					class:"confirm",
					content:{
						TextButton:{id:ok},
						TextButton:{id:cancel}
					}
				}
			]
		}
	}
}
```


#### Right click context menu for RepoView:
```json
[
	"New group":{"action":"newGroup"}
	"New repo":{"action":"newRepo"}
	"Cut":{"action":"cut"}
	"Paste":{"action":"paste"}
	
]
```



#### End-note:

Customization is key to creating an artisan experience. But customization should not get in the way of DRY: Don't repeat your self. If you have too much customization you can't easily pivot your product. Abstraction allows you to only focus on what is core in your app. 