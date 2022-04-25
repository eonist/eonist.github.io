Diffable table<!--more-->

### Research
- Manual section + uitable + diffable source: https://blog.mallow-tech.com/2021/01/diffable-datasource-in-tableview-and-collection-view-in-ios/
- UITableViewDiffableDataSource: https://developer.apple.com/documentation/uikit/uitableviewdiffabledatasource
- Manual section + UITable + diffable source (with github code): https://alfianlosari.medium.com/using-diffable-data-source-ios-13-api-in-uitableview-47343c2332be
- diffable table Example https://wwdcbysundell.com/2019/diffable-data-sources-first-look/
- Diffable data source (a lot of good detailed info):  https://www.donnywals.com/modern-table-views-with-diffable-data-sources/
- Diffable data source + section https://stackoverflow.com/questions/63981508/uitableview-diffable-data-source
- For macOS: NSTableViewDiffableDataSource https://developer.apple.com/documentation/appkit/nstableviewdiffabledatasource
- For macOS NSCollectionView: https://developer.apple.com/documentation/appkit/nscollectionviewdiffabledatasource
- Search with diffable: ✨ https://dev.to/ahmed_komsan12/getting-started-with-diffable-data-source-2581

### Keep your identifiers simple and to the point
When you choose your identifiers for your diffable data source and snapshot, try to make sure they only include data that will be rendered or influences the rendering. When I was stuck with reloading my cells, I was using NSManaged object subclasses to drive my data source. This seems to work well enough because everything was there. Items were added, removed and reordered. However, for some reason, my data source never seemed to pick up changes to the properties of my managed objects. I eventually got around this by providing a struct that contained the data I wanted to render rather than the entire managed object. A nice way I've found to define these structs is as extensions on the models themselves:

```swift
extension MyModel {
  struct Diffable {
    let id: UUID
    let title: String
    let subtitle: String
    // other properties that will be rendered on the cell
    init(model: MyModel) {
      self.id = model.id
      self.title = model.title
      self.subtitle = model.subtitle
    }
  }
}
```

### Always create typealiases of datasources:
```swift
fileprivate typealias UserDataSource     = UICollectionViewDiffableDataSource<ViewController.Section, Contact>
fileprivate typealias DataSourceSnapshot = NSDiffableDataSourceSnapshot<ViewController.Section, Contact>
```

When using this approach, you'd replace the MyModel item identifier with MyModel.Diffable, and when you create your snapshot, you must convert all MyModel instances to MyModel.Diffable. Since the initializer for the MyModel.Diffable object takes an instance of MyModel this is fairly straightforward:

### Hide in-activce sections:
- https://stackoverflow.com/questions/28947139/how-to-hide-uitableviewsections-in-swift
- https://stackoverflow.com/questions/6489731/hide-or-remove-section-from-uitableview
- https://medium.com/@novall/how-to-cleanly-hide-a-uitableview-section-4e11d43741e3

## Other links:
- Discussion regarding animation issues: https://stackoverflow.com/questions/58475481/how-to-get-a-diffable-snapshot-from-an-nsfetchresultscontroller-in-ios-13

### Gotchas:
- instead of storing data in an array, use a currentSnapshot variable as the one source of truth

### Resources
- Consider using type safe identifiers in model: https://www.swiftbysundell.com/articles/type-safe-identifiers-in-swift/
- Wrapper around row: https://stackoverflow.com/a/59790173/5389500
- Diffable data source utils: https://github.com/ivanvorobei/SPDiffable

### Thoughts:
- What about making the all `a-z` sections upfront. And then only showing the sections that has items. that way you dont have to generate the sections on the fly ✅

### Todo:
- Go through this: has a lot of diffable examples and demos: https://developer.apple.com/documentation/uikit/views_and_controls/collection_views/implementing_modern_collection_views
- And this: https://developer.apple.com/documentation/uikit/uiimage/building_high-performance_lists_and_collection_views
