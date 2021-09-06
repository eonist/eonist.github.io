Notes on NSDiffableSource + NSCollectionview<!--more-->

### Pretext:
- NSTableView has diffable source but doesn't have sections and we need sections for indexed headers
- NSCollectionView has section and diffable source so we use NSCollection
- We could setup our own sections in NSTable, but it might not work with showing hiding on demand, features we need

### Things to think about:
- NSCollectionView doesn't have recycling of cells? or? It does have register tho, so should work (it does recycle since el capitan)
- Using NSTable with custom code for the sections might be easy enough after all
- NSCollectionView is more complex than NSTable
- NSCollectionView seems to have issues resizing with of its subviews
- Ability to highlight items
- NSCollectionView has strange way of handling section headers, and issues with removing section headers, updating them etc

### Task to make SectionList
- Setup NSCollectionView with single column ✅
- Add Custom cells ✅
- Add section structure (multi-dim-array) ✅
- Add Section Headers ✅
- Add NSDiffableSource with some test cases, remove, add, etc 👈👈 🚫 (unable to update section headers)
- Add a-z section headers ✅
- Hide sectionHeaders if section has no rows ✅
- Hide scrollbars (see table project)
- Remake the project from scratch

### Gotchas:
- NSCollectionViewGridLayout is single sectioned layout. By Apple documentation, this means the layout can hold only one section. Change it to NSCollectionViewFlowLayout to get more than one sections.
- Quote from Apple Engineer "If you have mutable or complex data objects, you should not use them as item identifiers directly but rather just use your objects' identifiers with diffable data source and then fetch the correct object from your data store when configuring the cell by referencing the identifier.""

### Resources:
- For macOS: NSTableViewDiffableDataSource https://developer.apple.com/documentation/appkit/nstableviewdiffabledatasource
- For macOS NSCollectionView: https://developer.apple.com/documentation/appkit/nscollectionviewdiffabledatasource
- MacOS doesn't have section in NSTable, but NSCollectionView does: https://developer.apple.com/documentation/appkit/nscollectionview
- Or roll your own cell section in macOS. here is one approach: https://blog.krzyzanowskim.com/2015/05/29/lets-talk-about-sections-for-nstableview/
- Seems to support section headers for macOS: https://github.com/krzyzanowskim/NSTableView-Sections
- NSCollectionview tutorial for macos: https://www.raywenderlich.com/783-nscollectionview-tutorial
- NSCollectionview tutorial: https://www.raywenderlich.com/1047-advanced-collection-views-in-os-x-tutorial
- NIce: https://developer.apple.com/documentation/uikit/views_and_controls/collection_views/implementing_modern_collection_views
- Nice 2: https://developer.apple.com/documentation/uikit/uiimage/building_high-performance_lists_and_collection_views

### Example:
A bit messy, but has the important parts to get started
```swift
import Cocoa
import With

class SectionList: NSView {
   lazy var collectionView: NSCollectionView = createCollectionView()
   lazy var dataSource: DiffableDataSource = configureDataSource()
   var currentSnapshot: DiffableSnapshot? // we use this as the one source of truth
   lazy var scrollView: NSScrollView = createScrollView()
   init() {
      super.init(frame: .zero)
      self.wantsLayer = true
      self.layer?.backgroundColor = NSColor.systemGreen.cgColor
      _ = scrollView
      configureHeader()
      addItems()
   }
   /**
    * Add items
    */
   func addItems() {
      let a: [Film] = [.init(name: "Joe"), .init(name: "Goa")]
      let b: [Film] = [.init(name: "Hi"), .init(name: "Cava"), .init(name: "Demi")]
      let sectionItems: [FilmSection] = [.init(headerItem: .init(titleHeader: "Pro"), items: a), .init(headerItem: .init(titleHeader: "Amateur"), items: b)]
      let payloadDatasource = DataSource(sections: sectionItems)
      currentSnapshot = DiffableSnapshot() // DiffableDataSource()
      payloadDatasource.sections.forEach {
         currentSnapshot?.appendSections([$0])
         currentSnapshot?.appendItems($0.items, toSection: $0)
      }
      self.dataSource.apply(currentSnapshot!, animatingDifferences: true)
   }
   /**
    * Clears one section
    */
   func clear() {
      let snapshot = self.dataSource.snapshot()
      currentSnapshot = snapshot//NSDiffableDataSourceSnapshot<SectionItem<Header, [Film]>, Film>() // DiffableDataSource()
      currentSnapshot?.deleteItems(snapshot.sectionIdentifiers[0].items)
      currentSnapshot?.deleteSections([snapshot.sectionIdentifiers[0]]) // []
      self.dataSource.apply(currentSnapshot!, animatingDifferences: true)
   }
   /**
    * Boilerplate
    */
   required init?(coder: NSCoder) {
      fatalError("init(coder:) has not been implemented")
   }
}
extension SectionList: NSCollectionViewDelegateFlowLayout {
   private func configureDataSource() -> DiffableDataSource {
      return NSCollectionViewDiffableDataSource.init(collectionView: collectionView, itemProvider: { collectionView, indexPath, film  in
         let cell: Cell = collectionView.makeItem(withIdentifier: .init(rawValue: "Cell"), for: indexPath) as! Cell
         let data = film // sectionData[indexPath.section].rows[indexPath.item] // - Fixme: ⚠️️ add some asserts here or?
         cell.setData(name: data.name)
         // cell.configureCell(with: film)
         return cell
      })
   }
   private func configureHeader() {
      self.dataSource.supplementaryViewProvider = { (collectionView: NSCollectionView, kind: NSCollectionView.SupplementaryElementKind, indexPath: IndexPath) -> NSSection? in
         Swift.print("supplementaryViewProvider kind: \(kind) indexPath: \(indexPath)")
         if kind == NSCollectionView.elementKindSectionHeader {
//            let numberOfSections = collectionView.dataSource?.numberOfSections?(in: collectionView)
            let view = collectionView.makeSupplementaryView(ofKind: kind, withIdentifier: .init("SectionHeader"), for: indexPath)
            if let section = self.currentSnapshot?.sectionIdentifiers[indexPath.section] {
               (view as? SectionHeader)?.titleTextField.stringValue = "\(section.headerItem.titleHeader)"
            }
            return view as? NSSection
         } else if kind == NSCollectionView.elementKindSectionFooter {
            return nil
         } else {
            return nil
         }
      }
   }
   /**
    * CollectionView
    */
   func createCollectionView() -> NSCollectionView {
      Swift.print("createCollectionView")
      let layout: NSCollectionViewFlowLayout = with(.init()) {
         $0.minimumLineSpacing = 0
         $0.minimumInteritemSpacing = 0
         $0.scrollDirection = .vertical // this might be default?
      }
      return with(.init()) {
         $0.delegate = self
         $0.collectionViewLayout = layout
         $0.backgroundColors = [.clear]
         $0.isSelectable = true
         $0.register(Cell.self, forItemWithIdentifier: .init(rawValue: "Cell"))
         $0.register(SectionHeader.self, forSupplementaryViewOfKind: NSCollectionView.elementKindSectionHeader, withIdentifier: .init(rawValue: "SectionHeader"))
      }
   }
   /**
    * ScrollView
    * - Note: configure wrapping scroll (necessary for support collection view's scrolling)
    */
   func createScrollView() -> NSScrollView {
      let scrollView = NSScrollView()
      scrollView.documentView = collectionView
      self.addSubview(scrollView)
      scrollView.anchorAndSize(to: self)
      return scrollView
   }
   /**
    * sizeForItemAt
    */
   func collectionView(_ collectionView: NSCollectionView, layout collectionViewLayout: NSCollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> NSSize {
      return .init(width: collectionView.frame.size.width, height: 40)
   }
   // header
   func collectionView(_ collectionView: NSCollectionView, layout collectionViewLayout: NSCollectionViewLayout, referenceSizeForHeaderInSection section: Int) -> NSSize {
      return NSSize(width: collectionView.frame.size.width, height: 20)
   }
   // footer, this is called
   func collectionView(_ collectionView: NSCollectionView, layout collectionViewLayout: NSCollectionViewLayout, referenceSizeForFooterInSection section: Int) -> NSSize {
      return .zero
   }
   /**
    * didSelectItemsAt
    */
   func collectionView(_ collectionView: NSCollectionView, didSelectItemsAt indexPaths: Set<IndexPath>) {
      Swift.print("didSelectItemsAt: \(indexPaths)")
      self.clear()
   }
   /**
    * React on selection
    */
   func collectionView(_ collectionView: NSCollectionView, didDeselectItemsAt indexPaths: Set<IndexPath>) {
      Swift.print("didDeselectItemsAt: \(indexPaths)")

   }
}
typealias FilmSection = SectionItem<Header, [Film]>
struct SectionItem<U: Hashable, T: Hashable>: Hashable {
   let headerItem: U
   let items: T
}
struct Film: Decodable, Hashable {
   let name: String
}
struct Header: Hashable {
   let titleHeader: String
}
struct DataSource<T: Hashable> {
   let sections: [T]
}
typealias DiffableDataSource = NSCollectionViewDiffableDataSource<SectionItem<Header, [Film]>, Film>
typealias DiffableSnapshot = NSDiffableDataSourceSnapshot<SectionItem<Header, [Film]>, Film>

```
### Other tidbits:
- potential solution: https://github.com/nemecek-filip/CompositionalDiffablePlayground.ios
- and: https://nemecek.be/blog/62/how-to-refresh-headerfooter-with-diffable-data-source
- interesting discussion  https://developer.apple.com/forums/thread/126742
- gd https://www.raywenderlich.com/8241072-ios-tutorial-collection-view-and-diffable-data-source
-  try this project:  https://jamesrochabrun.medium.com/uicollectionviewdiffabledatasource-and-decodable-step-by-step-6b727dd2485
- and https://github.com/jamesrochabrun/UICollectionViewDiffableDataSource
