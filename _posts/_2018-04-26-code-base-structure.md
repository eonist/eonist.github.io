
### code structure
(source topology)
- app
   - src
      - utils (static utilitity methods)
         - extensions (regex,array,string,number,async,network)
      - libs (feature modules, data containers etc)
         - ricochet
      - manager (Singleton navigation, consts)
         - Config.swift (toggle debug/release)
         - Nav.swift (enum based app navigation)
      - view (views hierarchy and controller hierarchy)
         - balance
         - send
         - receive
         - swipe
         - prefs
         - dialog
   - frameworks (third-party libs and frameworks)
      - CoreBitcoin (non-binary)
   - assets.bundle
      - .pdf (vector icon assets etc)
      - .icns (app icon)
      - .json (dev & pub user config)
- readme.md