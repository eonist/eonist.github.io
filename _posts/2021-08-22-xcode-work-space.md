Notes on xcode workspace<!--more-->

### Gotchas
- To add an existing xCode projects to a workspace, drag the .xcodeproj file into the workspace
- To add a new xCode project, select the workspace in the project creation session
- local packages can be dragged in to xCode targets, drag the entire package folder (⚠️️   Remember to add the frameworks under the general pan as well or you will get a module not found warning etc ⚠️️)
- iOS and MacOS xCode projects can live on their own outside a workspace
- Using a workspace is a great way to debug iOS and MacOS apps at the same time. something that otherwise can be impossible if they relay on the same package etc
