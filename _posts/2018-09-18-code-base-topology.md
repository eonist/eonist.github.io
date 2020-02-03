My notes on source code topology<!--more-->

### Code structure (hierarchical)
(source topology)
- app
   - src
      - common (code that is common across modules, style, models, data, consts)
      - util (static utilitity methods)
         - extensions (spesific math, general network stuff etc)
      - manager (Singleton navigation, state of the app)
         - Config.swift (toggle debug/release)
         - Nav.swift (enum based app navigation)
      - view (views hierarchy and controller hierarchy)
         - account
         - send
         - receive
         - prefs
            - general
            - account
         - dialog
            - error
            - confirm
            - quit
   - lib (third-party libs and frameworks)
      - CoreMail (non-binary)
   - asset.bundle
      - .pdf (vector icon assets etc)
      - .icns (app icon)
      - .json (dev & pub user config)
- readme.md (⚠️️ Very important, on-boarding is crucial ⚠️️)


### Code-structure (flat)

├─ Models
├─ Views
├─ Controllers (or ViewModels, if your architecture is MVVM)
├─ Stores
├─ Helpers


##### Stores
At the "ground level" of a mobile app is usually some kind of model storage, that keeps its data in places such as on disk, in a local database, or on a remote server. This layer is also useful to abstract away any activities related to the vending of model objects, such as caching.

Whether it means kicking off a backend request or deserializing a large file from disk, fetching data is often asynchronous in nature. Your store's API should reflect this by offering some kind of deferral mechanism, as synchronously returning the data would cause the rest of your app to stall.

Code-projects are like this:

### Messy code:
Imagine your a restaurant and your making spaghetti bolognese for your guests. After the evening has passed, and the guest has gone home, you clean the restaurant for the next guests the day after. Now most coding projects make spaghetti for their guests to. But they never have time to cleanup. So everyday they just make more spaghetti in the same old pans and with the same old knifes. After a while the kitchen starts to be really messy, and crust starts to form everywhere. Until one day. Everything is so messy your basically just a spaghetti blob.

### Code hierarchy vs flat-pools:
Imagine if wikipedia was just 1 page with millions of links. It would be impossible to read. Many coders write their apps like this. Having all their code in Avery few files in very few folders. Folders with 50+ files, files with 1000lines of code. Its unreadable, unmanageable, imposing le to maintain. Instead try to create hierarchies of code. Like how wikipedia is structured. There is structure in the hierarchy, there is no structure in pools of random files.
