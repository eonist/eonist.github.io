My notes on modular programming<!--more-->

"And you can easily swap blocks. Want a new camera in the app? change the camera framework, api remains more or less the same"

### Overview:
- Modular: single 'responsibility'.
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
