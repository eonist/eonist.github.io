<!--more-->

### Manually:
```swift
struct Person {
  var age: Int
  var gender: Gender
  var height: Double
  var weight: Double
  //....
}

extension Person {
  init(managedObject: PersonEntity) {
    self.age = managedObject.age
    self.gender = Gender(rawValue: managedObject.gender)
    self.height = managedObject.height
    self.weight = managedObject.weight
    //...
  }
}

extension PersonEntity {
  init (valueObject: Person) {
    self.setValue(valueObject.age, forKey: "age")
    self.setValue(
      valueObject.gender.rawValue, forKey: "gender")
    self.setValue(valueObject.height, forKey: "height")
    self.setValue(valueObject.weight, forKey: "weight")
    //...
  }
}
```

### Semi automatically:
Using **default protocol implementations**, **generics** and **key paths**