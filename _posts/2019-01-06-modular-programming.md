My notes on modular programming<!--more-->

"And you can easily swap blocks. Want a new camera in the app? Change the camera framework, API remains more or less the same"

### Overview:
- Modular: single 'responsibility'
- monolith:  tightly coupled
- abstract systems into manageable layers

### Benefits of modular programming:
- Speed of implementing new stuff,
- Modularity and easily change components of the app,
- Scalability of the team, easier to find it’s way around, make programmers happy
- Modules are reusable across projects
- Each one have a independent test app, tests (and repo of course)
- Carthage, new functionality goes into a framework. For instance if you make a new password strength checker -> own framework
- Keeping time-saving-extensions in classes makes them prone to becoming out of sync with other parts of the code
- Many modern apps often have 20-40 internal .framework dependencies
- Divided project into logical parts that are living their life.
- Developers can run and develop only specific parts of the app (specific framework) without compiling everything.
- More flexibility on the project. A framework can be quite easily taken out or linked back into the project.
- Develop more efficiently using XCPlaygrounds as a project cannot be imported to the XCPlaygrounds but a framework can. A perfect use case is for developing UIComponents. With Playgrounds there is almost an instant build of the view.
- Every framework contains its unit tests so it can be run in isolation from the rest of the app.
- Developers can work more async, isolated on their own tasks without disturbing progress of the main project
- Modules can live isolated while other modules move on by using legacy versions of  dependencies
- Access control (limit with the internal accessor, only make public methods that can be accessed from outside)
- Shorter more explicit naming of classes: Not `RemoteImageAccessManipulator` but rather just internal `AccessManipulator` in a RemoteImage module


Nails monolith vs modular: [https://gist.github.com/joepie91/7f03a733a3a72d2396d6](https://gist.github.com/joepie91/7f03a733a3a72d2396d6)

### Additional reading:
- Low app startup time: [https://developer.apple.com/videos/play/wwdc2016/406/](https://developer.apple.com/videos/play/wwdc2016/406/)
- MonoRepo vs module: [https://github.com/babel/babel/blob/master/doc/design/monorepo.md](https://github.com/babel/babel/blob/master/doc/design/monorepo.md)
- Nails modular iOS project [https://edit.theappbusiness.com/modular-ios-strangling-the-monolith-4a6843a28992](https://edit.theappbusiness.com/modular-ios-strangling-the-monolith-4a6843a28992) 
