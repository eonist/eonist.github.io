Notes on swiftdata<!--more-->

Index:

- [Architecture](#architecture)
- [CRUD](#crud)
- [Model](#model)
- [For preview](#for-preview)
- [Access to data](#access-to-data)
- [Model container](#model-container)
- [ModelConfiguration](#modelconfiguration)
- [Context](#context)
- [Fetch discriptor](#fetch-discriptor)
- [Predicate and FetchDescriptor](#predicate-and-fetchdescriptor)
- [Relation](#relation)
- [History tracking](#history-tracking)
- [Migration](#migration)
- [Migration schema](#migration-schema)
- [Migration Plan](#migration-plan)
- [Add Migration Plan to Container](#add-migration-plan-to-container)
- [SortDescriptor](#sortdescriptor)
- [How Can I Implement a Sectioned Fetch in SwiftData](#how-can-i-implement-a-sectioned-fetch-in-swiftdata)
- [Animation](#animation)
- [Codable custom data types](#codable-custom-data-types)
- [Enum in the model](#enum-in-the-model)
- [@Attribute macro](#attribute-macro)
- [Non-persistent Data with the @Transient Macro](#non-persistent-data-with-the-transient-macro)
- [How to see images in SwiftData with computed properties](#how-to-see-images-in-swiftdata-with-computed-properties)
- [Defining Relationships and Delete Rules in SwiftData](#defining-relationships-and-delete-rules-in-swiftdata)
- [How do I store data separately from my database in SwiftData?](#how-do-i-store-data-separately-from-my-database-in-swiftdata)
- [Getting the File Location of a SwiftData Store](#getting-the-file-location-of-a-swiftdata-store)
- [iCloud](#icloud)
- [@Query](#query)
- [Coredata vs swiftdata](#coredata-vs-swiftdata)
- [AutoSave](#autosave)
- [Missing Features in swiftData vs coredata](#missing-features-in-swiftdata-vs-coredata)
- [Transformable Types](#transformable-types)
- [Todo](#todo)

- The power of `SwiftData`` lies in it's ability to streamline and simplify the complex task of data management, so you can focus more on building unique functionalities for your app.
- Not only does this type specify the Swift model but it also specifies the schema of the underlying Core Data store. This is execellent and makes data persistence much simpler.
- Apple's suggested way of using SwiftData in SwiftUI is using the @Query property wrapper and passing a ModelContext to the view using the modelContext environment value.
- SwiftData will automatically create a Schema from your model file. Your model will also have PersistentModel conformance: `protocol PersistentModel : AnyObject, Observable, Hashable, Identifiable`
- SwiftData allows you to take advantage of Swift data types. You can define properties as optional and non-optional. If you use non-optional values, make sure to set default values.
- You can store custom types in SwiftData as long as you conform them to the Codable protocol. 
- SwiftData also enhances the model by automatically conferring it the PersistentModel conformance. This means that your model is now Identifiable, Observable, and Hashable. The Observable protocol is especially useful because it ensures live updates when the model is used in SwiftUI views.
- Can I use SwiftData together with CoreData in the same project? SwiftData and CoreData are compatible. You can configure your storage so that both SwiftData and CoreData references the same SQLight database file.

```swift
struct EntryListView: View {
    @Environment(\.modelContext) private var modelContext
    @Query private var models: [EntryModel]

    var body: some View {
        List {
            ForEach(models) { model in
                Text(entry.date, style: \.date)
            }
        }
    }
}
```

### Architcture:
The SwiftData stack refers to the collection of framework classes that work together to provide the object graph management and persistence capabilities of the SwiftData framework. It consists of four main components:

- **Model** Classes: The model classes define the schema for your SwiftData application. It defines your data model’s entities (or tables), including their attributes, relationships, and constraints.
- **ModelContainer**: The model container is the broker between the model context and the persistent store (or stores). It creates a database from a given schema. It manages the reading and writing of data to and from disk and it also can handle iCloud sync.
- **ModelContext**: The context is like a working scratch pad for your application. When you fetch, create, or delete managed objects, you do so in the context. It’s also the object that tracks changes to model objects and offers methods for saving those changes to the container, which saves it to the underlying database.
- **Database**: This is the actual physical storage file. SwiftData uses an SQLite database. For more options, you can look at Core Data which can also use XML, binary files, or in-memory stores. With both CoreData and SwiftData, you never have to directly talk to the database. This is all done for you by the container.

### Crud:

To work with your models you use the model context. For example to **fetch** all folders in the database, fetch the data from the context:

```swift
var folders = try context.fetch(FetchDescriptor<Folder>()) // inline fetch 
```

You **create** new objects by inserting them in the model context:

```swift
let folder = Folder(name: "new folder")
context.insert(folder)
```
Similarly, you **delete** objects by asking the context to delete them:

```swift
context.delete(folder)
```
**Updating** is even more simple. You can set the new values for the objects directly. Here is an example where I change the title of the folder:

```swift
folder.title = "new title"
``` 

### Model
- Supports complex value types like Struct, Enum, Codable etc.
- You can control how properties are inferred and you can set clear constraints like @Attribite(.unique) or @Relationship
- Using @Transient we can exclude properties `@Transient var tripViews: Int = 0`
- model class is bindable in views: `@Bindable var card: Card`

This updates core-data: 
```swift
import SwiftUI

struct SnippetDetailView: View {
    @Bindable var snippet: Snippet // Snipper is a model class instance

    var body: some View {

      Toggle(snippet.isFavorite ? "favorite" : "not", 
             isOn: $snippet.isFavorite)
      ...
   }
}
```

```swift 
@Model class Song {
  var title: String
  var artist: String
  var album: String
  var genre: String
  var rating: Double
}
 
@Model class Album {
  @Attribute(.unique) var name: String
  var artist: String
  var genre: String
 
  // The cascade relationship instructs SwiftData to delete all 
    // songs when the album is deleted.
  @Attribute(.cascade) var songs: [Song]? = []
}
```
or
```swift
import SwiftData

@Model
class Person {
  @Attribite(.unique) var id: Int
  var name: String
  @Relationship(.cascade) var hobbies: [Hobby] // this is now: @Relationship(deleteRule: .cascade)
}
```

Or a more compex model:
```swift
import Foundation
import SwiftData

@Model final class Snippet {

    let uuid: UUID
    let creationDate: Date
    var code: String
    var image: Data?
    var isFavorite: BooL
    var notes: String
    var title: String
    var language: String?
    var folder: Folder? = nil
    var tags: [Tag]? = nil
    
     init(
        code: String = "",
        image: Data? = nil,
        isFavorite: Bool = false,
        language_: String? = nil,
        notes: String = "",
        title: String = "",
        folder: Folder? = nil,
        tags_: [Tag] = []
    ) {
        self.creationDate = Date()
        self.uuid = UUID()
        self.image = image
        self.code = code
        self.isFavorite = isFavorite
        self.language_ = language_
        self.notes = notes
        self.title = title

        self.folder = folder
        self.tags = tags
    }
}
```

Note that I am setting the ´creationDate´ and ´uuid´ properties in the initializer. You could add the default values directly to the properties like so:
```swift

@Model final class Snippet {
    let uuid: UUID = UUID()
    let creationDate: Date = Date()
    ...
}

```
By default a SwiftData ModelContainer will create its underlying storage in a file called `default.store`. If you want to change this so you can use an existing Core Data SQLite file, you can point your container to that file instead:
```swift
// Basic
let container = try ModelContainer(for: [Song.self, Album.self])
 
// With configuration
let container = try ModelContainer(for: [Song.self, Album.self], 
                                    configurations: ModelConfiguration(url: URL("path")))) // it seems path must be: appSupport dir and name must be "default.store"

```
In SwiftUI, you can set up the model container at the root of the application:


Once you have set up the model container, you can begin using the model context to fetch and save data. The context serves as your interface for tracking updates, fetching data, saving changes, and even undoing those changes. When working with SwiftUI, you can typically obtain the model context from your view’s environment:

```swift
struct ContextView: View {
    @Environment(\.modelContext) private var modelContext // access to context in a view
}
```
We mark the todoItems array with the @Query property wrapper. This @Query property automatically fetches the required data for you. In the provided code, we specify to fetch the ToDoItem instances. Once we retrieve the to-do items, we utilize the List view to display the items.

```swift
import SwiftData
import SwiftUI
 
@main
struct MusicApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer (for: [Song.self, Album.self]))
    }
}
```

### For preview:
```swift
#Preview {
    ContentView()
        .modelContainer(for: ToDoItem.self)
}
```

more advance preview setup:
```swift
let previewContainer: ModelContainer = {
    do {
        let container = try ModelContainer(for: Snippet.self,
                                           configurations: ModelConfiguration(isStoredInMemoryOnly: true))
        
        Task { @MainActor in // accessing context
            
            let context = container.mainContext
            
            let snip = Snippet.example2()

            
            let folder = Folder(name: "folder with favorite snippet")
            context.insert(folder)
            folder.snippets.append(Snippet(isFavorite: true, 
                     title: "favorite snippet"))

            // add test data here
        }
        
        return container
    } catch {
        fatalError("Failed to create container: \(error.localizedDescription)")
    }
}()
```

### Access to data:
```swift
@Query var todoItems: [ToDoItem] //
@Query(sort: \.artist, order: .reverse) var songs: [Song]
``` 

### Model container:
- The container will only be created once. There is only one instance. You cannot change any of the configuration parameters.
- SwiftUI has a convenient modifier to create a model container. In the main app file, add a modelContainer modifier: or in the preview code block



```swift
.modelContainer(for: [ToDo.self]) // init the database
```

```swift
// ModelContainer initialized with just Trip
let container = try ModelContainer(for: Trip.self)

// SwiftData infers related model classes as well
let container = try ModelContainer(
    for: [
        Trip.self, 
        BucketListItem.self, 
        LivingAccommodation.self
    ]
)
```

Two databases in one container:

```swift
@main
struct TripsApp: App {
    let fullSchema = Schema([ // holistic container schema
        Trip.self, 
        BucketListItem.self,
        LivingAccommodations.self,
        Person.self, 
        Address.self
    ])
  
    let trips = ModelConfiguration( // db1
        schema: Schema([ // schema related to  db1
            Trip.self,
            BucketListItem.self,
            LivingAccommodations.self
        ]),
        url: URL(filePath: "/path/to/trip.store"),
        cloudKitContainerIdentifier: "com.example.trips"
    )
  
    let people = ModelConfiguration( // db2
        schema: Schema([ // schema related to  db2
            Person.self, 
            Address.self
        ]),
        url: URL(filePath: "/path/to/people.store"),
        cloudKitContainerIdentifier: "com.example.people"
    )
  
    let container = try ModelContainer(for: fullSchema, trips, people) // multiple configs in one container
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(container)
    }
}
```
- The container will only be created once. There is only one instance. You cannot change any of the configuration parameters. 
- One big advantage to creating your configuration by hand is that it allows us to disable saving entirely – if you have particularly sensitive data, or perhaps you’ve shipped some template data that shouldn’t be changed under any circumstances. `allowsSave`

```swift
func modelContainer(
    for modelType: PersistentModel.Type,
    inMemory: Bool = false,
    isAutosaveEnabled: Bool = true, // this seems to not exist anymore
    isUndoEnabled: Bool = false,
    onSetup: @escaping (Result<ModelContainer, Error>) -> Void = { _ in }
) -> some View
```

### ModelConfiguration:
Configuring a ModelContainer

You can specify how your container is configured with the ´ModelConfiguration´. For example, setting up a temporary SwiftData store can be done with the following configuration:
```swift

let schema = Schema([Folder.self, Snippet.self, Tag.self])
let configuration = ModelConfiguration(inMemory: true)
let container = try! ModelContainer(for: schema, configurations: [configuration])
```
Here is the full list of options for the ModelConfiguration:

```swift

ModelConfiguration(
    _ name: String? = nil,
    schema: Schema? = nil,
    inMemory: Bool = false,
    readOnly: Bool = false,
    sharedAppContainerIdentifier: String? = nil,
    cloudKitContainerIdentifier: String? = nil
)
```
Another example would be to add a default database in the apps bundle and use this to show users example data. Here is how you would get this container:

```swift
let schema = Schema([Folder.self, Snippet.self, Tag.self])
let configuration = ModelConfiguration(url: "myBundelURL", inMemory: true)
let container = try! ModelContainer(for: schema, configurations: [configuration])
```

You can create and pass a configured container in SwiftUI. The following is a simple example, where I set the cloud kit container from above:

```swift
import SwiftUI
import SwiftData

@main
struct SnippetBoxApp: App {

    var container: ModelContainer? = {
        let conf = ModelConfiguration(cloudKitContainerIdentifier: "iCloud.com.karinprater.snippetbox")
        do {
            let container = try ModelContainer(for: Snippet.self, conf)
            return container
        } catch {
            print("errror: \(error)")
            return nil
        }
    }()

    var body: some Scene {
        WindowGroup {
            if let container = container {
                ContentView()
                //  .modelContainer(for: Snippet.self)
                    .modelContainer(container)
            } else {
                Text("Upps")
            }

        }
    }
}
```

### Context
```swift
 @Query var trips: [Trip] // works like an array
 @Query(sort: \.startDate, order: .reverse) var sortedTrips: [Trip] // works like an array
 @Environment(\.modelContext) var modelContext
  ForEach(trips) { trip in
    Button("delete") {
        modelContext.delete(trip)
    }
  }
  
```

Working with the ModelContext. The container has a main context property, which you can access like so:

```swift
@MainActor
var mainContext: ModelContext { get }
```

```swift
@Environment(\.modelContext) private var context
``` 

You can also create new contexts from the container:
```swift
let extraContext = ModelContext(container)
```

Changes to the ´extraContext´ are not visible to the main context. Only when you call save on the context, will the changes be visible. This behavior is similar to creating a child context in Core Data.

Deleting SwiftData Objects

Similar to CoreData you use the context to delete objects in CoreData:

```swift

@Environment(\.modelContext) private var context

private func delete(folder: Folder) {
    context.delete(folder)
}

```
Also, all objects have a context property that gives you the context they belong to. Here is how you could use it to streamline you delete function:

```swift

private func delete(folder: Folder) {
   if let context = folder.modelContext {
     context.delete(folder)
   }
}
```

**Accessing a speccific context** (multiple database support)
ref: https://developer.apple.com/forums/thread/733074
I haven't tried this out but I think it should work something like that:

Create in your parent view the new context.
```swift
let container = try! ModelContainer(for: Model.self,
                                    ModelConfiguration(inMemory: true)) // or whatever works for you
let localContext = ModelContext(container)
```
Then attach the localContext to a child view.
```swift
yourView
  .modelContext(localContext)
```
In the child view you should have access to it via the environment.
```swift
@Environment(\.modelContext) var modelContext
```


### Fetch discriptor: 
```swift
let context = self.newSwiftContext(from: Trip.self)
var trips = try context.fetch(FetchDescriptor<Trip>())
```


### Predicate and FetchDescriptor:
```swift
#Predicate and FetchDescriptor
let context = self.newSwiftContext(from: Trip.self)
let hotelNames = ["First", "Second", "Third"]

var predicate = #Predicate<Trip> { trip in // ✨ really cool
    trip.livingAccommodations.filter {
        hotelNames.contains($0.placeName)
    }.count > 0
}

var descriptor = FetchDescriptor(predicate: predicate)
var trips = try context.fetch(descriptor)

context.enumerate(FetchDescriptor<Trip>()) { trip in
    // Operate on trip
}
``` 

```swift
context.enumerate(
    descriptor,
    batchSize: 10000 // figure our what batch size represents
) { trip in
    // Remind me to make reservations for trip
}
```

Compound predicate:

```swift
let titleExpression = PredicateExpressions.Value("Back to the Future")
let yearExpression = PredicateExpressions.Value(1985)

let titlePredicate = PredicateExpressions.StartsWith(
   lhs: PredicateExpressions.KeyPath(\Movie.name),
   rhs: titleExpression
)

let yearPredicate = PredicateExpressions.Equal(
   lhs: PredicateExpressions.KeyPath(\Movie.year),
   rhs: yearExpression
)

let compoundPredicate = PredicateExpressions.And(
   lhs: titlePredicate,
   rhs: yearPredicate
)

let predicate = Predicate<Movie>({ input in
   compoundPredicate(input)
})
```

### Relation:

```swift
@Relationship(.cascade)
var bucketList: [BucketListItem]? = []
```

```swift
import Foundation
import SwiftData
@Model final public class Folder {

    var creationDate: Date
    var name: String
    var uuid: UUID

    @Relationship(.cascade, inverse: \Snippet.folder) var snippets: [Snippet]

    init(name: String = "",
         snippets: [Snippet] = []) {
        self.creationDate = Date()
        self.uuid = UUID()

        self.name = name
        self.snippets = snippets
    }
}
```

When you create objects that you want to add a relationship to already stored objects, you dont need to insert it in the context. SwiftData understands that you add an object to the context. Here is an example, where I create a new Snippet instance and add it to a folder:

```swift
let folder: Folder

func addItem() {
    let snippet = Snippet(title: "new snippet")
    folder.snippets.append(snippet)
}
```

### History tracking
More here: https://www.avanderlee.com/swift/persistent-history-tracking-core-data/ and https://github.com/fatbobman/PersistentHistoryTrackingKit
```swift
let url = URL(fileURLWithPath: "/path/to/Trips.store")

if let description = container.persistentStoreDescriptions.first {
    description.url = url
    description.setOption(true as NSNumber, forKey: NSPersistentHistoryTrackingKey)
}
```


### Migration:
From:
```swift
@Model 
final class Trip {
    @Attribute(.unique) var name: String
    var destination: String
    var start_date: Date
    var end_date: Date
    
    var bucketList: [BucketListItem]? = []
    var livingAccommodation: LivingAccommodation?
}
```
To:
```swift
@Model 
final class Trip {
    @Attribute(.unique) var name: String
    var destination: String
    @Attribute(originalName: "start_date") var startDate: Date
    @Attribute(originalName: "end_date") var endDate: Date
    
    var bucketList: [BucketListItem]? = []
    var livingAccommodation: LivingAccommodation?
}
```
### Migration schema
```swift
enum SampleTripsSchemaV1: VersionedSchema {
    static var models: [any PersistentModel.Type] { // storage of model
        [Trip.self, BucketListItem.self, LivingAccommodation.self]
    }

    @Model
    final class Trip {
        var name: String
        var destination: String
        var start_date: Date
        var end_date: Date
    
        var bucketList: [BucketListItem]? = []
        var livingAccommodation: LivingAccommodation?
    }

    // Define the other models in this version...
}

enum SampleTripsSchemaV2: VersionedSchema {
    static var models: [any PersistentModel.Type] {
        [Trip.self, BucketListItem.self, LivingAccommodation.self]
    }

    @Model
    final class Trip {
        @Attribute(.unique) var name: String
        var destination: String
        var start_date: Date
        var end_date: Date
    
        var bucketList: [BucketListItem]? = []
        var livingAccommodation: LivingAccommodation?
    }

    // Define the other models in this version...
}

enum SampleTripsSchemaV3: VersionedSchema {
    static var models: [any PersistentModel.Type] {
        [Trip.self, BucketListItem.self, LivingAccommodation.self]
    }

    @Model
    final class Trip {
        @Attribute(.unique) var name: String
        var destination: String
        @Attribute(originalName: "start_date") var startDate: Date
        @Attribute(originalName: "end_date") var endDate: Date
    
        var bucketList: [BucketListItem]? = []
        var livingAccommodation: LivingAccommodation?
    }

    // Define the other models in this version...
}
```

### migration plan:
```swift
enum SampleTripsMigrationPlan: SchemaMigrationPlan {
    static var schemas: [any VersionedSchema.Type] {
        [SampleTripsSchemaV1.self, SampleTripsSchemaV2.self, SampleTripsSchemaV3.self]
    }
    
    static var stages: [MigrationStage] {
        [migrateV1toV2, migrateV2toV3]
    }

    static let migrateV1toV2 = MigrationStage.custom(
        fromVersion: SampleTripsSchemaV1.self,
        toVersion: SampleTripsSchemaV2.self,
        willMigrate: { context in
            let trips = try? context.fetch(FetchDescriptor<SampleTripsSchemaV1.Trip>())
                      
            // De-duplicate Trip instances here...
                      
            try? context.save() 
        }, didMigrate: nil
    )
  
    static let migrateV2toV3 = MigrationStage.lightweight(
        fromVersion: SampleTripsSchemaV2.self,
        toVersion: SampleTripsSchemaV3.self
    )
}
```

add migration plan to container:

```swift
struct TripsApp: App {
    let container = ModelContainer(
        for: Trip.self, 
        migrationPlan: SampleTripsMigrationPlan.self
    )
    
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(container)
    }
}
```

### SortDescriptor
 
> How to sort data in SwiftData

You can customize the way data is fetched with @Query. This is the most basic version:

`@Query var folders: [Folder]`
However, the order of the folders will not be consistent. I found that it can vary between launches and view appearances. It is thus best to specify how you want the resulting fetch sorted like so:

`@Query(sort: \Folder.creationDate, order: .forward) var folders: [Folder]`
Alternatively, you can use the new SortDescriptor type to do the same:

`@Query(sort: [SortDescriptor(\Folder.creationDate)]) var folders: [Folder]`

```swift
@Query(filter: #Predicate<Snippet> { $0.title.contains("test") },
             sort: [SortDescriptor(\Snippet.creationDate)] )
    var snippets: [Snippet]
```
or
```swift
@Query(filter: #Predicate<Snippet> { $0.creationDate > startDate && $0.creationDate < endDate })
var snippets: [Snippet]
```

We can filter the query and only show a section with only the favorite snippets.

```swift
@Query(filter: #Predicate<Snippet>  { $0.isFavorite },
           sort: [SortDescriptor(\.creationDate)] )
    var favoriteSnippets: [Snippet]
``` 

Example: How to filter for non-nil properties

Snippet has an ´image´  property that is of type optional Data. If I would want to filter the snippet list and only show snippets with an image attached, I would use the following query:

```swift
@Query(filter: #Predicate<Snippet> { $0.image != nil },
         sort: [SortDescriptor(\Snippet.creationDate)] )
var imageSnippets: [Snippet]
```

or 

```swift
let descriptor = FetchDescriptor<SomeType>(sortBy: [SortDescriptor(\.date)])
```

**Complex Predicate Expressions**

Predicates can be further enhanced by combining multiple conditions and nesting expressions. This allows you to build more complex queries with precise filtering and sorting requirements. SwiftData supports the use of closures, enabling you to create nested expressions and combine them within the predicate.
```swift
@Query(filter: #Predicate<Snippet> { snippet in
    snippet.title.contains("test") &&
    (snippet.creationDate > startDate || snippet.modifiedDate > startDate)
})
```
In the above example, we filter snippets based on the condition that their titles contain “test” and either the creation date or modified date is greater than `startDate`.

In the following is a predicate where I search for snippets that contain any tags that have a name equal to “funny tag name”:
```swift

let predicate = #Predicate<Snippet> { snippet in
    snippet.tags?.contains {
        $0.name == "funny tag name"
    } == true
}

let descriptor = FetchDescriptor(predicate: predicate) // finds all type with predicte
let typedDescriptor = FetchDescriptor<SomeType>(predicate: predicate) // bound to type
// we can also add sortBy in the FetchDescriptor init
```

Searching for Conditions with Relationships

Sometimes you want to search for objects with a certain relationship. For example, I want to query all snippets belonging to a specific folder. The following example uses the intializer to updated the query to the selected folder´s snippets:

```swift
import SwiftUI
import SwiftData

struct SnippetListView: View {

    let folder: Folder

    @Query(sort: \Snippet.creationDate, order: .reverse)
    var snippets: [Snippet]

    init(for folder: Folder,) {
    
        let id = folder.uuid  // need to extract this first   

        self._snippets = Query(filter: #Predicate {
              $0.folder?.uuid == id
        }, sort: \.creationDate)


        self.folder = folder
    }

    var body: some View {
        List(snippets) { snippet in
             SnippetRow(snippet: snippet)
        }
    }
}
```

How to use predicates with optional values

Note that when working with optionals the predicates often don’t work when using optional chaining. You need to return a non-optional boolean. For example, let’s say I want to filter all snippets that are in folders that contain the string “new folders”. The following statement would return an optional boolean:

$0.folder?.name.contains("new folder")
whereas the following code, although looking slightly overcomplicated returns a boolean, and the SwiftData query will work:

```swift
@Query(filter: #Predicate<Snippet> {
    $0.folder?.name.contains("new folder") == true
},sort: [SortDescriptor(\.creationDate)] )
var newFolderSnippets: [Snippet]
```

Case Insensitive Filtering

This is another example, where I search for snippets that are in folders with the title “new folders”.

```swift
@Query(filter: #Predicate<Snippet> {
    $0.folder?.name == "new folder" 
},sort: [SortDescriptor(\.creationDate)] )
var newFolderSnippets: [Snippet]
```
I would like to show you how to support case insensitive filtering. However, this is not supported with predicate currently:

```swift
@Query(filter: #Predicate<Snippet> {     
     $0.title.localizedCaseInsensitiveContains("tEst")   // does not compile
}, sort: [SortDescriptor(\Snippet.creationDate)] ) 
var searchTermSnippets: [Snippet]
```
Example: Searching for snippets that are not attached to a folder

You can also check if certain properties are nil or not. For example, I can search for snippets that are not included in any folder with the following predicate:

```swift
@Query(filter: #Predicate<Snippet> { 
      $0.folder == nil 
},sort: [SortDescriptor(\Snippet.creationDate)] )
var nofolderSnippets: [Snippet]

```

Example: Filtering to show folders that are not empty

Another example is to search for objects that have relationships set or not. For example, I want to show all folders that are empty:

```swift
@Query(filter: #Predicate<Folder> { !$0.snippets.isEmpty  },
       sort: [SortDescriptor(\Folder.creationDate)] )
var snippetFolders: [Folder]

@Query(filter: #Predicate<Folder> { $0.snippets.count >= 2  },
       sort: [SortDescriptor(\Folder.creationDate)] )
```
var twoormoresnippetFolders: [Folder]
If you use an optional relationship, you would need to handle the non optional return like:
```swift

@Query(filter: #Predicate<Folder> { $0.snippets?.isEmpty == false  },
       sort: [SortDescriptor(\Folder.creationDate)] )
var snippetFolders: [Folder]
```

Example: How to fetch folders that contain favorite snippets

You can use the contains and allSatisfy sequence function to filter to-many relationships. For example, I can filter all folders that have any favorite snippets:
```swift

@Query(filter: #Predicate<Folder> {
    $0.snippets.contains {
        $0.isFavorite
    }
},sort: [SortDescriptor(\Folder.creationDate)] )
var someFavoriteSnippetFolders: [Folder]
```
or filter for folders that have only favorite snippets included and are not empty:
```swift

@Query(filter: #Predicate<Folder> {
    $0.snippets.allSatisfy {
        $0.isFavorite
    } && !$0.snippets.isEmpty
}, sort: [SortDescriptor(\Folder.creationDate)] )
var allFavoriteSnippetFolders: [Folder]
Similarly, I would handle optional relationships like:

@Query(filter: #Predicate<Folder> {
    $0.snippets?.contains {
        $0.isFavorite
    } == true // make this a boolean return value
},sort: [SortDescriptor(\Folder.creationDate)] )
var someFavoriteSnippetFolders: [Folder]
```

@Query has multiple initialisers where you can pass predicates for filtering and SortDesciptors. If I have a search text, I am using a predicate to search for snippets with titles containing this search term:

```swift
let predicate = #Predicate<Tag> { $0.name.contains(searchTerm) }
```

### How can I implement a sectioned fetch in SwiftData

SwifData query does not support sectioned fetches like ´@SectionedFetchRequest´ with Core Data. I think that this will likely be added in the next iteration with iOS 18.

As a workaround, you can generate multiple queries that will each represent a  section. For example, one section for favorite snippets and one for the rest:

```swift

@Query(filter: #Predicate<Snippet> { $0.isFavorite },
       sort: [SortDescriptor(\Snippet.creationDate)] )
private var favoriteSnippets: [Snippet]

@Query(filter: #Predicate<Snippet> { !$0.isFavorite },
       sort: [SortDescriptor(\Snippet.creationDate)] )
private var nonFavoriteSnippets: [Snippet]

```

fetchLimit:

```swift
let fetchDescriptor = FetchDescriptor<Movie>()
fetchDescriptor.fetchLimit = 1

do {
   let movies = try modelContext.fetch(fetchDescriptor)

   for movie in movies {
       print("Found \(movie.name)")
   }
} catch {
   print("Failed to load Movie model.")
}
```

### Animation:
```swift
@Query(sort: \Folder.creationDate, order: .forward, animation: .smooth) // now db changes animates the UI
var folders: [Folder]
```

### Codable custom data types:

```swift
struct ColorData: Codable {
    var red: Double = 1
    var green: Double = 1
    var blue: Double = 1
    var opacity: Double = 1

    var color: Color {
        Color(red: red, green: green, blue: blue, opacity: opacity)
    }

    init(red: Double, green: Double, blue: Double, opacity: Double) {
        self.red = red
        self.green = green
        self.blue = blue
        self.opacity = opacity
    }

    init(color: Color)  {
        let components = color.cgColor?.components ?? []

        if components.count > 0 {
            self.red  = Double(components[0])
        }

        if components.count > 1 {
            self.green = Double(components[1])
        }

        if components.count > 2 {
            self.blue = Double(components[2])
        }

        if components.count > 3 {
            self.opacity = Double(components[3])
        }
    }
}

@Model final public class Tag {
    ...
   var colorData: ColorData = ColorData(red: 1, green: 1, blue: 1, opacity: 1)
}
```

### Enum in the model:

You can directly use enum types with your SwiftData properties, as long as they conform to Codable. For example, I want to store the programming language for each code snippet. If the programming language is defined as an enum:
```swift

enum CodingLanguage: String, Codable, CaseIterable, Identifiable {
    case swift
    case objectivec
    case pyton
    case css
    case typescript

    var id: Self { return self }
}
```


You can directly use this in the Snippet model like so:

```swift

@Model final class Snippet {
    ...
   var codingLanguage: CodeEditor.Language = .swift
}

```

### @Attribute macro
`@attached(member) public macro Attribute(_ options: PropertyOptions..., originalName: String? = nil, hashModifier: String? = nil)`
Renaming a property in SwiftData with lightweight migration

You can rename a property with the @Attribute(originalName:) macro. For example I can rename my folder name property like so:

```swift
@Model final class Folder {
  @Attribute(originalName: "name") var newName: String
  ...
}
```
@Attribute macro with property options

The following property options are available:

- **unique**:  ensures the property’s value is unique across all models of the same type
- **transient**: enables the context to disregard the property when saving the owning model
- **transformable**: transforms the property’s value between an in-memory form and a persisted form
- **externalStorage**: stores the property’s value as binary data adjacent to the model storage
- **encrypt**: stores the property’s value in an encrypted form
- preserveValueOnDeletion: preserves the property’s value in the persistent history when the co**ntext** deletes the owning model
- **spotlight**: indexes the property’s value so it can appear in Spotlight search results
 
Adding Uniqueness Constraints to a Property in SwiftData

You can make a property in SwiftData unique like so:

```swift
@Model final class Folder {
     @Attribute(.unique) public var id: String
     ...
}
```
If you try to insert an object with the same value id, the existing object will be updated with the value properties of the object you wanted to insert. This behavior is called an insert.

For example, use a unique id for data in an app, where you fetch from the server and store locally in SwiftData. If you add the same object id again, the existing  object is updated. This can help you keep your app’s data up-to-date and consistent.

Unique constraints work with primate value types like Numeric, String, and UUID.


#### Non-persistent Data with the @Transient Macro

If you do not want to store property in your database and you only want to cash it, you can use the @Transient macro:

```swift
@Attribute(.transient)
var title: String = ""

```
or the @Attribute(.transient) macro like so:
```swift
@Transient
var title: String = ""
```
If you set this property, you can access the value as long as the app is running. When your user quotes and relaunches the app, the value is gone. You should provide a default value, as you cannot assure a value exists.


#### How to see images in SwiftData with computed properties

You can add computed properties to your model class. In the following, I am creating a computed property that generates a ´UIImage´ from the data stored in SwiftData:

```swift
import SwiftUI
import SwiftData

@Model final public class Snippet {
    ...
    
    var imageData: Data?
    
    var image: UIImage? {
        UIImage(data: imageData)
   }
}
```
Note that with the public Xcode release you no longer need to annotate computed properties with @Transient.

Storing data separately from your database

Large data should not be stored directly in your database because this can make your database slow. You can tell SwiftData to externally store property with the externalStorage property option. For example, I would want to store my snippet image externally:
```swift
@Attribute(.externalStorage)
var imageData: Data?
```

#### Defining Relationships and Delete Rules in SwiftData

SwiftData will automatically determine relationships from your model classes. As an example, I want a one-to-many relationship between Snippet and Folder. Snippet has a property pointing to Folder:

@Model final public class Snippet {
   var folder: Folder?
   ...
}
and folder has a property pointing to many Snippets that it can contain:

@Model final public class Folder {
   var snippets: [Snippet]
   ...
}

If you use classes for your property types, SwiftData will recognise these as relationships. On the other hand, if you use value types, it uses them as stored properties. ???? 

#### Many to many
As a second relationship, I want to have a many-to-many relationship between Tag and Snippet. Snippet has a property that points to many Tags:

@Model final public class Snippet {
    var tags: [Tag] = []
    ...
}
and Tag has a property holding reference to many Snippets:

@Model final public class Tag {
    var snippets: [Snippet] = []
    ...
}
Note that for the many-to-many relationship, I used non-optional arrays. If you want to use iCloud sync, you would need to change this to optional arrays.

#### Setting explicit inverse relationships

In some cases, you want to explicitly set what inverse property is used. In this case, you can use the @Relationship macro like so:
```swift

@Model final public class Snippet {

    @Relationship(inverse: \Tag.snippets)
    var tags: [Tag]? = nil
    ...
}
```

How to set delete rules in SwiftData

With the ´@Relationship´ macro you can set delete rules for your SwiftData relationships. For example, I want to delete all the snippets in a folder, when the folder is deleted. For that, I would set a cascading delete rule like so:
```swift
@Model final public class Folder {
    @Relationship(.cascade, inverse: \Snippet.folder)
    var snippets: [Snippet]
    ...
}
```

The options for delete rules are the same options available for CoreData:

- **cascade**: A rule that deletes any related objects.
- deny: A rule that prevents the deletion of an object because it contains one or more **references**  to other objects.
- **nullify**: A rule that nullifies the related object’s reference to the deleted object.
- **noAction**: A rule that doesn’t make changes to any related objects. You have to manually updated references to the deleted object. Otherwise, your data will be in an inconsistent state and may reference models that don’t exist.


### How do I store data separately from my database in SwiftData?

In SwiftData, you can store large data separately from your database using the ´@Attribute(.externalStorage)’ macro. It tells SwiftData to store the property in a separate file from your database file.

### Getting the File Location of a SwiftData Store

SwiftData will create a database file when the app first launches. You can get the location of the file that is located in the support directory like so:

```swift
func getDatabaseURL() -> URL? {
   guard let urlApp = FileManager.default.urls(for: .applicationSupportDirectory, in: .userDomainMask).last else { return nil }
   let url = urlApp.appendingPathComponent("default.store")
   if FileManager.default.fileExists(atPath: url.path) {
      print("swiftdata db at \(url.absoluteString)")
      return url
   } else {
      return nil
   }
}
```
The file is of type  .store. It uses an SQLite database underneath.

### iCloud;
You can then use your container identifier for iCloud to setup a new SwiftData container:

```swift
let schema = Schema([Folder.self, Snippet.self, Tag.self])
let configuration = ModelConfiguration(cloudKitContainerIdentifier: "iCloud.com.karinprater.snippetbox")
let container = try! ModelContainer(for: schema, configurations: [configuration])
```
Note that the same model restrictions as for CoreData with iCloud sync are also valid: unique constraints are not supported and all relationships have to be optional.
- Go to project, your target and select new capabilities.
- Create a new container for iCloud sync. This is the identifier for your iCloud storage.

### @Query
SwiftData is made to work well with SwiftUI. For example, you can use the ´@Query´ property wrapper in your SwiftUI views to fetch data. In the following, I am fetching all folder objects sorted by the creation date:

```swift

import SwiftUI
import SwiftData
struct FolderListView: View {
    @Query(sort: \Folder.creationDate, order: .forward)
    var folders: [Folder]

    var body: some View {
        List {
             ForEach(folders) { folder in
                    NavigationLink(value: folder) {
                        FolderRow(folder: folder)
                    }
             }
        }
    }
}
```

### Coredata vs swiftdata:
```swift
// CoreData uses the managedObjectContext  from the environment:

@Environment(\.managedObjectContext) var context
// SwiftData uses the modelContext:

@Environment(\.modelContext) private var context
```
Fetching Data in SwiftData with @Query

Fetching data also gets a facelift with SwiftData. You can use a Query to fetch a list of objects from the SwiftData container instead of using a fetch request like in Core Data

This is the fetch you would use in CoreData:

```swift
@FetchRequest(sortDescriptors: [SortDescriptor(\Folder.creationDate_)]) private var folders: FetchedResults<Folder>
```
which you can now relate with SwiftData query:
```swift

import SwiftUI
import SwiftData

struct FolderListView: View {

    @Query(sort: \Folder.creationDate, order: .forward) var folders: [Folder]

    var body: some View {
        List {
            ForEach(folders) { folder in
                 FolderRow(folder: folder)
            }
        }
    }
}

```


CoreData observation and modification in subviews with @ObservedObject:
```swift

struct FolderRow: View {
   @ObservedObject var folder: Folder
   var body: some View {
    ...
   }
}
becomes in SwiftData:

struct FolderRow: View {
   @Binding var folder: Folder
   var body: some View {
    ...
   }
}
```

### AutoSave:
Saving changes in SwiftData

Unlike CoreData, you don’t have to explicitly call save on the context. In SwiftData save happens automatically when main view updates happen. You can remove all code that handles save actions in CoreData.

If you don’t like this behavior, you can change the container configuration like so:

```swift
.modelContainer(for: Snippet.self, isAutosaveEnabled: false)
```

### Missing Features in swiftData vs coredata:

SwiftData is in its first version and is thus missing quite a few features. Here is a list of what you might miss out on:

- sectioned queries are not supported
- limited support of predicates with relationships
- predicate support for case insensitive text filtering
- predicates with enum properties
- to-many relationships do not keep their order 
- iCloud sync works only with a private database
If you absolutely need some of these missing features, I would recommend working with CoreData - instead. CoreData is much more mature and is integrated well with SwiftUI.
- app crashes when you use redo enabled container and custom types or enums as properties
- to-many relationships with iCloud sync need to be optional. You have to work with optional arrays
to-many relationships are defined as arrays to other model classes. This makes the impression that they keep their sort order, but this is not the case, and the array is returned in - seemingly random order.
- app crashes after data migrations

### Transformable Types
SwiftData is not only capable of storing primitive types like (Int, String, Boolean etc) but also complex types. If you want to store a custom type in the database then you can use transformable types. Transformable types in SwiftData allow you to store non-standard data types, such as custom classes or objects, dictionaries, arrays, or any other complex data structure, as attributes in your SwiftData properties. SwiftData uses a process called “value transformation” to convert these custom data types into a format that can be stored in the persistent store (typically a SQLite database).

Consider a scenario that you want to store UIColor to the database using SwiftData. UIColor is a complex object and can be stored using transformable type. The implementation of the model is shown below:

```swift
@Model
class Room {
    var name: String
    @Attribute(.transformable(by: UIColorValueTransformer.self)) var color: UIColor
    
    init(name: String, color: UIColor) {
        self.name = name
        self.color = color
    }
}

```

The color property is using the @Attribute, which is indicating that it is a transformable type and will be using UIColorValueTransformer to convert the UIColor to data and vice versa. This conversion is needed to persist UIColor to the database. The implementation of the UIColorValueTransformer is shown below:

```swift
class UIColorValueTransformer: ValueTransformer {
    
    // return data
    override func transformedValue(_ value: Any?) -> Any? {
        guard let color = value as? UIColor else { return nil }
        do {
            let data = try NSKeyedArchiver.archivedData(withRootObject: color, requiringSecureCoding: true)
            return data
        } catch {
            return nil
        }
    }
    
    // return UIColor
    override func reverseTransformedValue(_ value: Any?) -> Any? {
        guard let data = value as? Data else { return nil }
        
        do {
            let color = try NSKeyedUnarchiver.unarchivedObject(ofClass: UIColor.self, from: data)
            return color
        } catch {
            return nil 
        }
    }
    
}

```

After you have implemented UIColorValueTransformer you need to register it for your application. This can be done in the App file as shown below:
```swift

@main
struct RoomsAppApp: App {
    
    init() {
        ValueTransformer.setValueTransformer(UIColorValueTransformer(), forName: NSValueTransformerName("UIColorValueTransformer"))
    }
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .modelContainer(for: [Room.self])
        }
    }
}
```

Now, you can persist color for your Room as shown below:
```swift

 Button(action: {
                let room = Room(name: name, color: UIColor(color))
                context.insert(room)
                name = ""
            }, label: {
                Text("Save")
                    .frame(maxWidth: .infinity)
            }).buttonStyle(.borderedProminent)
                .padding([.top], 20)
And also display the colors in the user interface as implemented below:

 List(rooms) { room in
                HStack {
                    Text(room.name)
                    Spacer()
                    Rectangle()
                        .fill(Color(uiColor: room.color))
                        .frame(width: 50, height: 50)
                        .clipShape(RoundedRectangle(cornerRadius: 10.0, style: .continuous))
                }
            }
```
Please keep in mind that saving a complex object to the database can have an impact on performance. It’s essential to make a judgment call based on your application’s specific requirements and performance considerations.

### Tips: 
- Toggle debug mode: `let inMemory: Bool = Configuration.mode == .unitTesting ? true : false`

### Gotchas:
- Don't override your model classes. It might be possible, but complexity is greater than the benefit. You can use a protocol as a way to enforce similar baseclass structure. 
- Making a property optional, doesnt break the database model
- Adding a new property breaks the database, use migrationschema or reset the database

### Resources:
- Copy a .store: https://www.hackingwithswift.com/quick-start/swiftdata/how-to-pre-populate-an-app-with-an-existing-swiftdata-database
- Example of using 2 backing stores in one container: https://medium.com/@priya_talreja/swiftdata-in-swiftui-part-2-7a17b06a328a
- Merging contexts: https://www.hackingwithswift.com/quick-start/swiftdata/how-to-merge-two-model-contexts
- a discussion on background threads in swiftdata: https://stackoverflow.com/questions/76468533/how-to-use-a-background-context-with-swiftdata-looking-for-a-coredata-automatic
- more background talk: https://developer.apple.com/forums/thread/731338
- Doing things properly on the background thread: https://useyourloaf.com/blog/swiftdata-background-tasks/
- Using persistent history tracking to respond to network database changes etc: https://itnext.io/how-to-observe-data-changes-in-swiftdata-using-persistent-history-tracking-77ceff2cb689
- SwiftData shortcommings: https://www.hackingwithswift.com/quick-start/swiftdata/common-swiftdata-errors-and-their-solutions
- multiple schemas in one container: https://www.hackingwithswift.com/quick-start/swiftdata/how-to-add-multiple-configurations-to-a-modelcontainer
- advance migration: https://www.hackingwithswift.com/quick-start/swiftdata/how-to-create-a-complex-migration-using-versionedschema
- multiple contexts: https://developer.apple.com/forums/thread/733074
- unit and ui testing for swiftdata: https://azamsharp.com/2023/07/04/the-ultimate-swift-data-guide.html
- persistent identifier: https://useyourloaf.com/blog/swiftdata-fetching-an-existing-object/
- background processing a database: https://www.hackingwithswift.com/quick-start/swiftdata/how-to-transfer-an-object-between-a-background-context-and-the-main-context
- sectioned queries: https://medium.com/@thomas.magis.agosta/sectioned-queries-in-swiftdata-with-sectionedquery-b1e23c733f26
- accessing cordata api from swiftdata: https://itnext.io/swiftdatakit-unleashing-advanced-core-data-features-in-swiftdata-3fcd5f443c99
- deep insight into swiftdata https://betterprogramming.pub/unveiling-the-data-modeling-principles-of-swiftdata-480c993d2f5c
- Predicate workarounds: https://stackoverflow.com/questions/76845726/combining-predicate-in-swiftdata and https://www.hackingwithswift.com/quick-start/swiftdata/filtering-the-results-from-a-swiftdata-query
- coredata and swiftdata together: https://www.hackingwithswift.com/quick-start/swiftdata/how-to-make-core-data-and-swiftdata-coexist-in-the-same-app
- ⭐ Use viewmodel with swiftui and swiftdata: https://www.hackingwithswift.com/quick-start/swiftdata/how-to-use-mvvm-to-separate-swiftdata-from-your-views
- ⭐ remote server data (not icloud) with codable and swiftdata: https://betterprogramming.pub/decodable-swiftdata-a-proof-of-concept-for-building-offline-first-ios-apps-5c5434ea61b5
- ⭐ swiftdata + codable: https://www.donnywals.com/making-your-swiftdata-models-codable/
- On fitering with predicates: https://www.swiftyplace.com/blog/fetch-and-filter-in-swiftdata

### Todo: 
- add links from issue to resources
- simplify the index
- write about schema version
- maybe leverage schema version code from coredata?