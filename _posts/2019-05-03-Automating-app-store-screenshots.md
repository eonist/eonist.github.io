My notes on automating screenshots for the app store<!--more-->.

> The quality on an app’s screenshot is fundamental in the decision of buying/downloading on the AppStore.

### Requisites:
1. Download and install Fastlane Snapshot for terminal: [https://github.com/fastlane/fastlane/releases](https://github.com/fastlane/fastlane/releases)  
2. Install brew: [https://brew.sh](https://brew.sh) (Needed for imagemagick)
3. Install imagemagick Terminal: `brew install imagemagick` (Need for FrameIt )
4. Install frameIt: `fastlane frameit`  

### Workflow:
1. Add the `Snapfile` to the project root folder
1. Terminal: `fastlane snapshot` And remember to add `setupSnapshot(app)` just before `app.launch` in the UITests
2. Terminal: `fastlane frameit download_frames`
3. Terminal: `fastlane framit` or `fastlane frameit silver` if you want the white iPhone bezel
4. Configure the Frameit.conf file. Use the MindNode example from the resource paragraph
5. Set the titles to match the .png names in `title.strings` and `keywords.strings`  (⚠️️ Edit these .strings files in XCode ⚠️️)

### Gotchas:
- To create multiline titles use the newline: `\n` character
- Edit the titles.strings file in xcode (other apps breaks the syntax)
- To only add frame with out title, just provide the default fastlane.json

### Tips:
- To reset all simulators: Terminal: `fastlane snapshot reset_simulators`

### Resources:
- FrameIt official docs: [https://docs.fastlane.tools/actions/frameit/](https://docs.fastlane.tools/actions/frameit/)
- FrameIt config file [https://github.com/fastlane/examples/tree/master/MindNode/screenshots](https://github.com/fastlane/examples/tree/master/MindNode/screenshots)
- Official doc for Fastlane-Snapshot: [https://docs.fastlane.tools/actions/snapshot/](https://docs.fastlane.tools/actions/snapshot/)
- Precheck official docs: [https://docs.fastlane.tools/actions/precheck/](https://docs.fastlane.tools/actions/precheck/)
- Using XCode-UI-testing:
- Great overview of all the Fastlane tools: [https://www.raywenderlich.com/233168-fastlane-tutorial-getting-started](https://www.raywenderlich.com/233168-fastlane-tutorial-getting-started)
- StatusMagic: [https://medium.com/@juli1quere/a-perfect-status-bar-on-screenshots-with-fastlane-84a94a350b2a](https://medium.com/@juli1quere/a-perfect-status-bar-on-screenshots-with-fastlane-84a94a350b2a)
- Shots is a sketch plugin that helps frame iphone screens. [https://github.com/jtholloran/shots](https://github.com/jtholloran/shots)
