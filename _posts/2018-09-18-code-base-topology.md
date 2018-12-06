My notes on source code topology<!--more-->

### Code structure
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
