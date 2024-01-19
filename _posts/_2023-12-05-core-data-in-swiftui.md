<!--more-->

You use an **NSPersistentContainer** instance to set up the **model**, **context**, and **store-coordinator** simultaneously.

1. An instance of **NSManagedObjectModel** describes your app’s types, including their properties and relationships.

2. An instance of **NSManagedObjectContext** tracks changes to instances of your app’s types.

3. An instance of **NSPersistentStoreCoordinator** saves and fetches instances of your app’s types from stores.





### Adding coredata to swiftUI (Via GUI)
https://betterprogramming.pub/how-to-make-a-task-list-using-swiftui-and-core-data-a8ab6eaf0f58
```swift 

struct ContentView: View {
    @Environment(\.managedObjectContext) var context
    // this is the variable we added
    @State private var taskName: String = ""
    var body: some View {
        // this is the TextField that we added
        TextField("Task Name", text: $taskName)
    }
}
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
var body: some View {
    HStack{
        TextField("Task Name", text: $taskName)
        Button(action: {
            self.addTask()
        }){
            Text("Add Task")
        }
    }
}
func addTask() {
    let newTask = Task(context: context)
    newTask.id = UUID()
    newTask.isComplete = false
    newTask.name = taskName
    newTask.dateAdded = Date()
    do {
        try context.save()
    } catch {
        print(error)
    }
}
func updateTask(_ task: task) {
    let isComplete = true
    let taskID = task.id! as NSUUID
    let fetchRequest: NSFetchRequest<NSFetchRequestResult> = NSFetchRequest(entityName: "Task")
    fetchRequest.predicate = NSPredicate(format: "id == %@", taskID as CVarArg)
    fetchRequest.fetchLimit = 1
    do {
        let test = try context.fetch(fetchRequest)
        let taskUpdate = test[0] as! NSManagedObject
        taskUpdate.setValue(isComplete, forKey: "isComplete")
    } catch {
        print(error)
    }
}
```


## Programmatic coredata:
- https://www.cocoanetics.com/2012/04/creating-a-coredata-model-in-code/

Prgrammatically creating the model:

```swift
internal var _model: NSManagedObjectModel {
    let model = NSManagedObjectModel()

    // Create the entity
    let entity = NSEntityDescription()
    entity.name = "DTCachedFile"
    // Assume that there is a correct 
    // `CachedFile` managed object class.
    entity.managedObjectClassName = String(CachedFile)

    // Create the attributes
    var properties = Array<NSAttributeDescription>()

    let remoteURLAttribute = NSAttributeDescription()
    remoteURLAttribute.name = "remoteURL"
    remoteURLAttribute.attributeType = .StringAttributeType
    remoteURLAttribute.optional = false
    remoteURLAttribute.indexed = true
    properties.append(remoteURLAttribute)

    let fileDataAttribute = NSAttributeDescription()
    fileDataAttribute.name = "fileData"
    fileDataAttribute.attributeType = .BinaryDataAttributeType
    fileDataAttribute.optional = false
    fileDataAttribute.allowsExternalBinaryDataStorage = true
    properties.append(fileDataAttribute)

    let lastAccessDateAttribute = NSAttributeDescription()
    lastAccessDateAttribute.name = "lastAccessDate"
    lastAccessDateAttribute.attributeType = .DateAttributeType
    lastAccessDateAttribute.optional = false
    properties.append(lastAccessDateAttribute)

    let expirationDateAttribute = NSAttributeDescription()
    expirationDateAttribute.name = "expirationDate"
    expirationDateAttribute.attributeType = .DateAttributeType
    expirationDateAttribute.optional = false
    properties.append(expirationDateAttribute)

    let contentTypeAttribute = NSAttributeDescription()
    contentTypeAttribute.name = "contentType"
    contentTypeAttribute.attributeType = .StringAttributeType
    contentTypeAttribute.optional = true
    properties.append(contentTypeAttribute)

    let fileSizeAttribute = NSAttributeDescription()
    fileSizeAttribute.name = "fileSize"
    fileSizeAttribute.attributeType = .Integer32AttributeType
    fileSizeAttribute.optional = false
    properties.append(fileSizeAttribute)

    let entityTagIdentifierAttribute = NSAttributeDescription()
    entityTagIdentifierAttribute.name = "entityTagIdentifier"
    entityTagIdentifierAttribute.attributeType = .StringAttributeType
    entityTagIdentifierAttribute.optional = true
    properties.append(entityTagIdentifierAttribute)

    // Add attributes to entity
    entity.properties = properties

    // Add entity to model
    model.entities = [entity]

    // Done :]
    return model
}
```



### Programmatic coredata:

- **NSEntityDescription** describes an entity in a model: name, list of properties, and NSManagedObject class.
- **NSAttributeDescription** allows to specify a type, default value, and other features of an attribute in an entity.
- **NSPropertyDescription** describes a property of an entity: name, is it transient, is it optional. Property is a base for attributes and relationships.
- **NSRelationshipDescription** describes relationships between two entities.


**1. NSManagedObject**
```swift
public final class ExampleEntity: NSManagedObject {
    @NSManaged var name: String
    @NSManaged var date: Date
}
```

**2. NSEntityDescription**
```swift
func entityDescription() -> NSEntityDescription {

    let entity = NSEntityDescription()
    entity.name = "ExampleEntity"
    entity.managedObjectClassName = NSStringFromClass(ExampleEntity.self)

    // Attributes
    let nameAttr = NSAttributeDescription()
    nameAttr.name = "name"
    nameAttr.attributeType = .stringAttributeType
    nameAttr.isOptional = true

    let dateAttr = NSAttributeDescription()
    dateAttr.name = "date"
    dateAttr.attributeType = .dateAttributeType

    entity.properties = [nameAttr,
                         dateAttr]

    return entity
}
```

**3. NSManagedObjectModel**
```swift
func managedObjectModel() -> NSManagedObjectModel {

    let model = NSManagedObjectModel()

    model.entities = [entityDescription()]

    return model
}

```
**4. NSPersistentContainer**
```swift
lazy var persistentContainer: NSPersistentContainer = {
    let container = NSPersistentCloudKitContainer(name: "ExampleModel", managedObjectModel: managedObjectModel())

    container.loadPersistentStores(completionHandler: { (storeDescription, error) in
        if let error = error as NSError? {
            fatalError("Unresolved error \(error), \(error.userInfo)")
        }
    })
    return container
}()
```

Will output:

**XML**
```xml

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<model type="com.apple.IDECoreDataModeler.DataModel" documentVersion="1.0" lastSavedToolsVersion="18154" systemVersion="20E241" minimumToolsVersion="Automatic" sourceLanguage="Swift" userDefinedModelVersionIdentifier="">
    <entity name="ExampleEntity" representedClassName="ExampleEntity" syncable="YES" codeGenerationType="class">
        <attribute name="name" optional="YES" attributeType="String"/>
        <attribute name="date" optional="YES" attributeType="Date" usesScalarValueType="NO"/>
    </entity>
</model>
``` 

### Setting up model construct:
```swift
// First we need to create entities
let authorEntity = NSEntityDescription()
authorEntity.name = "Author"
authorEntity.managedObjectClassName = NSStringFromClass(Author.self)

let publicationEntity = NSEntityDescription()
publicationEntity.name = "Publication"
publicationEntity.managedObjectClassName = NSStringFromClass(Publication.self)
publicationEntity.isAbstract = true

let articleEntity = NSEntityDescription()
articleEntity.name = "Article"
articleEntity.managedObjectClassName = NSStringFromClass(Article.self)

// Publication is a base entity for Article
publicationEntity.subentities = [ articleEntity ]

// Attributes
let authorFullName = NSAttributeDescription()
authorFullName.name = "fullName"
authorFullName.attributeType = .stringAttributeType

let publicationNumberOfViews = NSAttributeDescription()
publicationNumberOfViews.name = "numberOfViews"
publicationNumberOfViews.attributeType = .integer64AttributeType

let publicationPublicationDate = NSAttributeDescription()
publicationPublicationDate.name = "publicationDate"
publicationPublicationDate.attributeType = .dateAttributeType

let articleText = NSAttributeDescription()
articleText.name = "text"
articleText.attributeType = .stringAttributeType
articleText.isOptional = true

// Now we can create relationships
let authorToPublication = NSRelationshipDescription()
authorToPublication.name = "publications"
authorToPublication.destinationEntity = publicationEntity
authorToPublication.deleteRule = .cascadeDeleteRule

let publicationToAuthor = NSRelationshipDescription()
publicationToAuthor.name = "author"
publicationToAuthor.destinationEntity = authorEntity
publicationToAuthor.maxCount = 1
publicationToAuthor.deleteRule = .nullifyDeleteRule

// Inverse relationships
authorToPublication.inverseRelationship = publicationToAuthor
publicationToAuthor.inverseRelationship = authorToPublication

// Set properties
authorEntity.properties = [ authorFullName, authorToPublication ]
publicationEntity.properties = [ publicationNumberOfViews, publicationPublicationDate, publicationToAuthor ]
articleEntity.properties = [ articleText ]
``` 

Create the model: 

```swift
let model = NSManagedObjectModel()
model.entities = [ authorEntity, publicationEntity, articleEntity ]
```

This is how the object will look like:

```swift
final class Author: NSManagedObject {
    @NSManaged var name: String?
    @NSManaged public var publications: NSSet?
}

class Publication: NSManagedObject {
    @NSManaged var publicationDate: Date?
    @NSManaged var numberOfViews: Int64
    @NSManaged var author: Author?
}

final class Article: Publication {
    @NSManaged var text: String?
}
```

We can also do this with a declerative lib: https://github.com/dmytro-anokhin/core-data-model-description

```swift
let modelDescription = CoreDataModelDescription(
    entities: [
        .entity(
            name: "Author",
            managedObjectClass: Author.self,
            attributes: [
                .attribute(name: "name", type: .stringAttributeType)
            ],
            relationships: [
                .relationship(name: "publications", destination: "Publication", toMany: true, deleteRule: .cascadeDeleteRule, inverse: "author")
            ]),
        .entity(
            name: "Publication",
            managedObjectClass: Publication.self,
            attributes: [
                .attribute(name: "publicationDate", type: .dateAttributeType),
                .attribute(name: "numberOfViews", type: .integer64AttributeType, isOptional: true)
            ],
            relationships: [
                .relationship(name: "author", destination: "Author", toMany: false, inverse: "publications")
            ]),
        .entity(
            name: "Article",
            managedObjectClass: Article.self,
            parentEntity: "Publication",
            attributes: [
                .attribute(name: "text", type: .stringAttributeType)
            ])
    ]
)

let model = modelDescription.makeMode()
```

### Gotchas:
- create coredata in GUI then open the .xcdatamodeld xml file and convert that to coredata code

### Resources
- ⭐ Simple programmatic coredata: https://tigi44.github.io/ios/iOS,-Swift-Core-Data-Model-in-a-Swift-Package/
- ⭐ Setting up a programmatic stack (the new way): https://developer.apple.com/documentation/coredata/setting_up_a_core_data_stack
- ⭐ Has code for struct to coredata conversion: https://www.kodeco.com/books/real-world-ios-by-tutorials/v1.0/chapters/4-defining-the-data-layer-databases
- ⭐ Declarative core-data swift package: https://dmytro-anokhin.medium.com/core-data-and-swift-package-manager-6ed9ff70921a
- https://stackoverflow.com/a/71595595/5389500
- https://betterprogramming.pub/exploring-coredata-from-data-model-creation-to-managed-object-instances-3bc796b27b41
- not programatic but swiftui: https://www.kodeco.com/27201015-dynamic-core-data-with-swiftui-tutorial-for-ios
- setting up coredata programaticly the old way: https://developer.apple.com/documentation/coredata/setting_up_a_core_data_stack/setting_up_a_core_data_stack_manually
- swiftui and core data: https://www.kodeco.com/books/swiftui-cookbook/v1.0/chapters/5-integrate-core-data-with-swiftui
- fitlering with fetch in swiftui: https://www.hackingwithswift.com/books/ios-swiftui/dynamically-filtering-fetchrequest-with-swiftui
- coredata in swiftui: https://www.kodeco.com/9335365-core-data-with-swiftui-tutorial-getting-started
- coredata in swiftui: https://itnext.io/how-to-preview-a-swiftui-view-with-core-data-elements-in-xcode-b109d60a5ae0