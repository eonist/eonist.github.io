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
