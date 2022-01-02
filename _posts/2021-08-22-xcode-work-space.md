Notes on xcode workspace<!--more-->

### Gotchas
- To add an existing xcode projects to a workspace, drag the .xcodeproj file into the workspace
- To add a new xcode project, select the workspace in the project creation session
- local packges can be dragged in to xcode targets, drag the entire package folder
- iOS and MacOS xCode projects can live on their own outside a workspace
- Using a workspace is a great way to debug iOS and MacOS apps at the same time. something that otherwise can be impossible if they relay on the same package etc
