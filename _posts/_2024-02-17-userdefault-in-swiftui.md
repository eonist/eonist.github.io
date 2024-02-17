@propertyWrapper
struct UserDefault<Value> {
    let key: String
    let defaultValue: Value
    let container: UserDefaults = .standard

    var wrappedValue: Value {
        get {
            return container.object(forKey: key) as? Value ?? defaultValue
        }
        set {
            container.set(newValue, forKey: key)
        }
    }
}

Then we can use it as property and provide default value

final class KeepHistoryService {
    @UserDefault(key: "keepHistoryCheckDate", defaultValue: nil)
    var checkDate: Date?