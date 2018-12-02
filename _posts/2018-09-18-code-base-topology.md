My notes on source code topology<!--more-->
### Code structure
(source topology)
- app
   - src
      - util (static utilitity methods)
         - extensions (regex,array,string,number,async,network)
      - libs (feature modules, data containers etc)
         - TinyMail
      - manager (Singleton navigation, consts)
         - Config.swift (toggle debug/release)
         - Nav.swift (enum based app navigation)
      - view (views hierarchy and controller hierarchy)
         - account
         - send
         - receive
         - prefs
         - dialog
   - frameworks (third-party libs and frameworks)
      - CoreMail (non-binary)
   - assets.bundle
      - .pdf (vector icon assets etc)
      - .icns (app icon)
      - .json (dev & pub user config)
- readme.md
