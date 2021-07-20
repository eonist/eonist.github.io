My notes on alphabetically indexing an UITableView <!--more-->

### Gotchas:
- You populate section headers with letters to add support for indexed UITableView
- The issue with sections in an editable UITable is that you need a nested array. and then mapping that back and forth to a flat array is not trivial.
- It might be easier to keep a flat array as datasource, then create a dictionary with count of each section. then use the key in the dictionary as the section
- Turns out mapping between 1d and 2d array is better todo on edit, than on the fly. as you need speed when you render a tableview, but dont need speed when you edit a model

### Resources:
- The core idea: https://medium.com/swlh/indexing-your-uitableviews-aad93948776c
- Real example: https://www.ioscreator.com/tutorials/indexed-table-view-ios-tutorial-ios11
- Uses a model and is grouped by category: https://alisoftware.github.io/architecture/2018/05/20/dedicated-datasources/
- Uses diff method to update rows and section: https://stasost.medium.com/ios-aimate-tableview-updates-dc3df5b3fe07 and github: https://github.com/Stan-Ost/ReloadableTableView
- Using generics with section / row data: https://www.ralfebert.de/ios-examples/uikit/uitableviewcontroller/grouping-sections/

### Relevant API:
- `numberOfSectionsInTableView`: – returns the total number of sections in the table view. Usually we set the number of section to 1. If you want to have multiple sections, set this value to a larger number.
- `titleForHeaderInSection`: method – returns the header titles for different sections. This method is optional if you do not assign titles for the section.
- `numberOfRowsInSection`: method – returns the total number of rows in a specific section
- `cellForRowAtIndexPath`: method – this method shouldn’t be new to you if you know how to display data in UITableView. It returns the table data for a particular section.
- `sectionIndexTitlesForTableView`: method – returns the indexed titles that appear in the index list on the right side of the table view. For example, you can return an array of strings containing “A” to “Z”.
- `sectionForSectionIndexTitle`: method – returns the section index that the table view should jump to when user taps a particular index.

### Examples:
- Example of making an array of alphabetical letters from an array of cars (this can be done a lot simpler with .map etc)
- The idea here is then to create sections of all the letters (what if a car is deleted? is section updated?, we probably update the dictionary on model edits?)
- We also use the dictionary[key].values.count to populate rowsForSection etc

```swift
let cars = ["Audi", "Aston Martin", "BMW", "Bugatti", "Bentley", "Chevrolet", "Cadillac", "Dodge", "Ferrari", "Ford","Honda","Jaguar","Lamborghini","Mercedes", "Mazda","Nissan","Porsche","Rolls Royce","Toyota","Volkswagen"]
 // 1 The first letter is extracted from each car and is used as a key of the carsDictionary. With this key a new array of cars is created, or when the key exists the car item is appended to the array.
 for car in cars {
     let carKey = String(car.prefix(1))
         if var carValues = carsDictionary[carKey] {
             carValues.append(car)
             carsDictionary[carKey] = carValues
         } else {
             carsDictionary[carKey] = [car]
         }
 }
 // 2 The keys of the carsDictionary are sorted by alphabetical order.
 carSectionTitles = [String](carsDictionary.keys)
 carSectionTitles = carSectionTitles.sorted(by: { $0 < $1 })
```

### More modern example:
- Ref: https://stackoverflow.com/a/53628028/5389500
- And: https://newbedev.com/alphabetical-sections-in-table-table-view-in-swift
```swift
struct Section {
    let letter : String
    let names : [String]
}
let usernames = ["John", "Nancy", "James", "Jenna", "Sue", "Eric", "Sam"]
var sections = [Section]()
// group the array to ["N": ["Nancy"], "S": ["Sue", "Sam"], "J": ["John", "James", "Jenna"], "E": ["Eric"]]
let groupedDictionary = Dictionary(grouping: usernames, by: { String($0.prefix(1)) })
// get the keys and sort them
let keys = groupedDictionary.keys.sorted()
// map the sorted keys to a struct
sections = keys.map { Section(letter: $0, names: groupedDictionary[$0]!.sorted()) }
```

### Todo:
- Figure out how to add alphabetical index to MacOS (maybe model adds / removes ranges of indicies on alteration calls? pseudo code a bit to figure out how that would work)
- Figure out what: `UILocalizedIndexedCollection` does

### Bonus:
- diffable collapsable sections: https://swiftsenpai.com/development/reload-diffable-section-header/

### Nice to know:
- MacOS doesn't have section in NSTable, but NSCollectionView does: https://developer.apple.com/documentation/appkit/nscollectionview
- Or roll your own cell section in macOS. here is one approach: https://blog.krzyzanowskim.com/2015/05/29/lets-talk-about-sections-for-nstableview/
