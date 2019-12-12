My notes on UserDefaults<!--more-->. User defaults can be used for saving types such as: `Bool, Dictionary, Int, String, Data, Array`

### Example
```swift
// variables
let defaults = UserDefaults.standard
defaults.set(25, forKey: "Age")
defaults.set(true, forKey: "hasOnboarded")
defaults.set(CGFloat.pi, forKey: "Pi")
defaults.set("Paul Hudson", forKey: "Name")
defaults.set(Date(), forKey: "LastRun")
// Arr and dict:
let array = ["Hello", "World"]
defaults.set(array, forKey: "SavedArray")

let dict = ["Name": "Paul", "Country": "UK"]
defaults.set(dict, forKey: "SavedDict")
```

#### Reading object
```swift
UserDefaults.standard.set("John", forKey: "FirstName")
print(UserDefaults.standard.object(forKey: "FirstName"))//John
```

### Remove object
```swift
UserDefaults.standard.set(25, forKey: "Age")
print(UserDefaults.standard.object(forKey: "Age"))//25
UserDefaults.standard.removeObject(forKey: "Age")
print(UserDefaults.standard.object(forKey: "Age"))//nil
```

### Unwrap object
```swift
let array = defaults.object(forKey:"SavedArray") as? [String] ?? [String]()
let dict = defaults.object(forKey: "SavedDict") as? [String: String] ?? [String: String]()
```

### String
```swift
UserDefaults.standard.set("John", forKey: "name")
//Retrieving String
let name = UserDefaults.standard.string(forKey: “name”) ?? “”
```

### Bool
```swift
//Saving Boolean value
UserDefaults.standard.set(true, forKey: “userlogin”)
//Retrieving Boolean value
let status = UserDefaults.standard.bool(forKey: “userlogin”) ?? false
```

### The model approach
```swift
import Foundation
/**
 * Persistent storage for prefrences
 * ## Examples:
 * Defaults.nameAndAddress = (name: "John", address: "New York") // Saving details
 * let name = Defaults.nameAndAddress.name // Retrieving details
 * Defaults.clearUserData() // Clear details
 */
struct Storage {
   /**
    * - Fixme: ⚠️️ this should be try error etc ⚠️
    */
   static var nameAndAddress: NameAndAddress {
      get { // Returns the Dictionary for the userSessionKey
         let model: Model = {
            if let dict: [String: String] = (UserDefaults.standard.value(forKey: userSessionKey) as? [String: String]) {
               return Model(dict) // Convert dictionary to model
            } else {
               return defaultModel
            }
         }()
         return (model.name ?? "err", model.address ?? "err") // Convert model to tuple and return it
      }
      set { // Stores the Dictionary for the userSessionKey
         UserDefaults.standard.set([nameKey: newValue.name, addressKey: newValue.address], forKey: userSessionKey)
      }
   }
   /**
    * Removes the dictionary with the userSessionKey
    */
   static func clearUserData() {
      UserDefaults.standard.removeObject(forKey: userSessionKey)
   }
}
/**
 * TypeAlias
 */
extension Storage {
   typealias NameAndAddress = (name: String, address: String)
}

/**
 * Constants
 */
extension Storage {
   /**
    * The keys that is used in the dictionary (Dictionary is stored in the UserDefaults.standard only)
    */
   static let (nameKey, addressKey) = ("name", "address")
   /**
    * This is the unique key that ensures that no other part of the app overwrites this (key,value) pair in the user-defaults file
    */
   static let userSessionKey = "io.github.futurelab.\("UUID")" // add user uuid here
   /**
    * Default model if none is set yet
    */
   static var defaultModel: Model = {
      let name: String = "Adam"
      let address: String = "Boston"
      return .init([nameKey: name, addressKey: address])
   }()
}
/**
 * Model
 */
extension Storage {
   struct Model {
      var name: String?
      var address: String?
      init(_ dict: [String: String]) {
         self.name = dict[nameKey]
         self.address = dict[addressKey]
      }
   }
}

```

### Interacting with the Model:
```swift
Defaults.nameAndAddress = (name: "John", address: "New York") // Saving details
let name = Defaults.nameAndAddress.name // Retrieving details
Defaults.clearUserData() // Clear details
```

### Extension example
```swift
extension UserDefaults {
   /**
    * ## Examples:
    * UserDefaults.name
    */
    class var name: String? {
        get { return standard.string(forKey: "name") }
        set { standard.set(newValue, forKey: "name") }
    }
}

extension UserDefaults {
   /**
    * ## Examples:
    * UserDefaults.stringDateDictionary
    */
    class var stringDateDictionary: [String: Date]? {
        get { return standard.object(forKey: "stringDateDictionary") as? [String:Date] }
        set { standard.set(newValue, forKey: "stringDateDictionary") }
    }
}
```

### Other supported types are
- `stringArray(forKey: String) -> [String]?` // Returns the array of strings associated with the specified key.
- `data(forKey: String) -> Data?`
- `url(forKey: String) -> URL?`//Returns the URL associated with the specified key.
- `object(forKey: String) -> Any?`👈 ✨//Returns the object associated with the specified key.
- `integer(forKey: String) -> Int` //Returns the integer value associated with the specified key.
- `float(forKey: String) -> Float` //Returns the float value associated with the specified key.
- `double(forKey: String) -> Double` //Returns the double value associated with the specified key.
- `dictionaryRepresentation() -> [String : Any]` //Returns a dictionary that contains a union of all key-value pairs in the domains in the search list.

### Important
- integer, double, returns `0`
- bool returns `false`

### Extra
- `didChangeNotification` // allows you to get notifications when there is a change in the data of the userDefaults (only for the thread you currently operate at)
- Create a custom UserDefault instance: `init?(suiteName: String?)` //Creates a user defaults object initialized with the defaults for the specified database name.
   - `func addSuite(named: String)` //Inserts the specified domain name into the receiver’s search list.
   - `func removeSuite(named: String)` //Removes the specified domain name from the receiver’s search list.
   - `func register(defaults: [String : Any])` //Adds the contents of the specified dictionary to the registration domain. (basically replace entire UserDefaults)

### Resources
- Apple doc on UserDefaults: https://developer.apple.com/documentation/foundation/userdefaults
- My personal swift-tool-box press `T` and write userdefault.swift and you will find a small Extension https://github.com/eonist/swift-utils

### Notes:
This is where the UserDeaults plist is stored: `rootOfApplication/Library/Preferences/com.yourcompany.appName.plist`
