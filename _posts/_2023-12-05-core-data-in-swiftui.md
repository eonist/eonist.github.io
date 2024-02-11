My notes on core data in swiftUI<!--more-->

You use an **NSPersistentContainer** instance to set up the **model**, **context**, and **store-coordinator** simultaneously.

1. An instance of **NSManagedObjectModel** describes your app’s types, including their properties and relationships.

2. An instance of **NSManagedObjectContext** tracks changes to instances of your app’s types.

3. An instance of **NSPersistentStoreCoordinator** saves and fetches instances of your app’s types from stores.


### Adding coredata to swiftUI (Via GUI)
https://betterprogramming.pub/how-to-make-a-task-list-using-swiftui-and-core-data-a8ab6eaf0f58
```swift 
struct MainView: View {
    @Environment(\.managedObjectContext) var managedContext
    // this is the variable we added
    @State private var itemName: String = ""
    var body: some View {
        // this is the TextField that we added
        TextField("Item Name", text: $itemName)
    }
}
struct MainView_Previews: PreviewProvider {
    static var previews: some View {
        MainView()
    }
}
var body: some View {
    HStack{
        TextField("Item Name", text: $itemName)
        Button(action: {
            self.createItem()
        }){
            Text("Add Item")
        }
    }
}
func createItem() {
    let newItem = Item(context: managedContext)
    newItem.id = UUID()
    newItem.isComplete = false
    newItem.name = itemName
    newItem.dateAdded = Date()
    do {
        try managedContext.save()
    } catch {
        print(error)
    }
}
func updateItem(_ item: Item) {
    let isComplete = true
    let itemID = item.id! as NSUUID
    let fetchRequest: NSFetchRequest<NSFetchRequestResult> = NSFetchRequest(entityName: "Item")
    fetchRequest.predicate = NSPredicate(format: "id == %@", itemID as CVarArg)
    fetchRequest.fetchLimit = 1
    do {
        let result = try managedContext.fetch(fetchRequest)
        let itemUpdate = result[0] as! NSManagedObject
        itemUpdate.setValue(isComplete, forKey: "isComplete")
    } catch {
        print(error)
    }
}
```


## Programmatic coredata:
- https://www.cocoanetics.com/2012/04/creating-a-coredata-model-in-code/

Prgrammatically creating the model:

```swift
internal var _dataModel: NSManagedObjectModel {
    let dataModel = NSManagedObjectModel()

    // Define the entity
    let dataEntity = NSEntityDescription()
    dataEntity.name = "CachedDocument"
    // Assume that there is a correct 
    // `CachedDocument` managed object class.
    dataEntity.managedObjectClassName = String(CachedDocument)

    // Define the attributes
    var attributeProperties = Array<NSAttributeDescription>()

    let urlAttribute = NSAttributeDescription()
    urlAttribute.name = "url"
    urlAttribute.attributeType = .StringAttributeType
    urlAttribute.optional = false
    urlAttribute.indexed = true
    attributeProperties.append(urlAttribute)

    let documentDataAttribute = NSAttributeDescription()
    documentDataAttribute.name = "documentData"
    documentDataAttribute.attributeType = .BinaryDataAttributeType
    documentDataAttribute.optional = false
    documentDataAttribute.allowsExternalBinaryDataStorage = true
    attributeProperties.append(documentDataAttribute)

    let lastOpenedDateAttribute = NSAttributeDescription()
    lastOpenedDateAttribute.name = "lastOpenedDate"
    lastOpenedDateAttribute.attributeType = .DateAttributeType
    lastOpenedDateAttribute.optional = false
    attributeProperties.append(lastOpenedDateAttribute)

    let expiryDateAttribute = NSAttributeDescription()
    expiryDateAttribute.name = "expiryDate"
    expiryDateAttribute.attributeType = .DateAttributeType
    expiryDateAttribute.optional = false
    attributeProperties.append(expiryDateAttribute)

    let documentTypeAttribute = NSAttributeDescription()
    documentTypeAttribute.name = "documentType"
    documentTypeAttribute.attributeType = .StringAttributeType
    documentTypeAttribute.optional = true
    attributeProperties.append(documentTypeAttribute)

    let documentSizeAttribute = NSAttributeDescription()
    documentSizeAttribute.name = "documentSize"
    documentSizeAttribute.attributeType = .Integer32AttributeType
    documentSizeAttribute.optional = false
    attributeProperties.append(documentSizeAttribute)

    let tagIdentifierAttribute = NSAttributeDescription()
    tagIdentifierAttribute.name = "tagIdentifier"
    tagIdentifierAttribute.attributeType = .StringAttributeType
    tagIdentifierAttribute.optional = true
    attributeProperties.append(tagIdentifierAttribute)

    // Add attributes to entity
    dataEntity.properties = attributeProperties

    // Add entity to model
    dataModel.entities = [dataEntity]

    // Done :]
    return dataModel
}
```



### Programmatic coredata:

- **NSEntityDescription** describes an entity in a model: name, list of properties, and NSManagedObject class.
- **NSAttributeDescription** allows to specify a type, default value, and other features of an attribute in an entity.
- **NSPropertyDescription** describes a property of an entity: name, is it transient, is it optional. Property is a base for attributes and relationships.
- **NSRelationshipDescription** describes relationships between two entities.


**1. NSManagedObject**
```swift
// Define a final class for a SampleEntity that inherits from NSManagedObject
public final class SampleEntity: NSManagedObject {
    // Define a managed variable for the title of the entity
    @NSManaged var title: String
    // Define a managed variable for the timestamp of the entity
    @NSManaged var timestamp: Date
}
```

**2. NSEntityDescription**
```swift
// Function to define the entity description
func itemDescription() -> NSEntityDescription {

    // Create a new entity description
    let itemEntity = NSEntityDescription()
    // Set the name of the entity
    itemEntity.name = "SampleItem"
    // Set the class name of the entity
    itemEntity.managedObjectClassName = NSStringFromClass(SampleItem.self)

    // Define the attributes
    // Create a title attribute
    let titleAttr = NSAttributeDescription()
    // Set the name of the attribute
    titleAttr.name = "title"
    // Set the type of the attribute
    titleAttr.attributeType = .stringAttributeType
    // Set the optionality of the attribute
    titleAttr.isOptional = true

    // Create a timestamp attribute
    let timestampAttr = NSAttributeDescription()
    // Set the name of the attribute
    timestampAttr.name = "timestamp"
    // Set the type of the attribute
    timestampAttr.attributeType = .dateAttributeType

    // Set the properties of the entity
    itemEntity.properties = [titleAttr,
                             timestampAttr]

    // Return the entity
    return itemEntity
}
```

**3. NSManagedObjectModel**
```swift
// Function to create and return a managed object model
func createManagedObjectModel() -> NSManagedObjectModel {

    // Create a new managed object model
    let dataModel = NSManagedObjectModel()

    // Set the entities of the model
    dataModel.entities = [itemDescription()]

    // Return the model
    return dataModel
}

```
**4. NSPersistentContainer**
```swift
// Define a lazy variable for the persistent storage container
lazy var storageContainer: NSPersistentContainer = {
    // Create a container with the name "SampleModel" and the managed object model returned by createManagedObjectModel()
    let container = NSPersistentCloudKitContainer(name: "SampleModel", managedObjectModel: createManagedObjectModel())

    // Load the persistent stores
    container.loadPersistentStores(completionHandler: { (storeDescription, error) in
        // If there is an error, print it and terminate the application
        if let error = error as NSError? {
            fatalError("Unresolved error \(error), \(error.userInfo)")
        }
    })
    // Return the container
    return container
}()
```

Will output:

**XML**
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<model type="com.apple.IDECoreDataModeler.DataModel" documentVersion="1.0" lastSavedToolsVersion="18154" systemVersion="20E241" minimumToolsVersion="Automatic" sourceLanguage="Swift" userDefinedModelVersionIdentifier="">
    <!-- Define an entity with the name "SampleEntity" -->
    <entity name="SampleEntity" representedClassName="SampleEntity" syncable="YES" codeGenerationType="class">
        <!-- Define an attribute with the name "title" of type String -->
        <attribute name="title" optional="YES" attributeType="String"/>
        <!-- Define an attribute with the name "timestamp" of type Date -->
        <attribute name="timestamp" optional="YES" attributeType="Date" usesScalarValueType="NO"/>
    </entity>
</model>
``` 

### Setting up model construct:
```swift
// Initialize entities
let writerEntity = NSEntityDescription()
writerEntity.name = "Writer"
writerEntity.managedObjectClassName = NSStringFromClass(Writer.self)

let contentEntity = NSEntityDescription()
contentEntity.name = "Content"
contentEntity.managedObjectClassName = NSStringFromClass(Content.self)
contentEntity.isAbstract = true

let blogEntity = NSEntityDescription()
blogEntity.name = "Blog"
blogEntity.managedObjectClassName = NSStringFromClass(Blog.self)

// Content is a base entity for Blog
contentEntity.subentities = [ blogEntity ]

// Define attributes
let writerFullName = NSAttributeDescription()
writerFullName.name = "fullName"
writerFullName.attributeType = .stringAttributeType

let contentViewCount = NSAttributeDescription()
contentViewCount.name = "viewCount"
contentViewCount.attributeType = .integer64AttributeType

let contentPublishDate = NSAttributeDescription()
contentPublishDate.name = "publishDate"
contentPublishDate.attributeType = .dateAttributeType

let blogBody = NSAttributeDescription()
blogBody.name = "body"
blogBody.attributeType = .stringAttributeType
blogBody.isOptional = true

// Define relationships
let writerToContent = NSRelationshipDescription()
writerToContent.name = "contents"
writerToContent.destinationEntity = contentEntity
writerToContent.deleteRule = .cascadeDeleteRule

let contentToWriter = NSRelationshipDescription()
contentToWriter.name = "writer"
contentToWriter.destinationEntity = writerEntity
contentToWriter.maxCount = 1
contentToWriter.deleteRule = .nullifyDeleteRule

// Set inverse relationships
writerToContent.inverseRelationship = contentToWriter
contentToWriter.inverseRelationship = writerToContent

// Assign properties
writerEntity.properties = [ writerFullName, writerToContent ]
contentEntity.properties = [ contentViewCount, contentPublishDate, contentToWriter ]
blogEntity.properties = [ blogBody ]
``` 

Create the model: 

```swift
let model = NSManagedObjectModel()
model.entities = [ authorEntity, publicationEntity, articleEntity ]
```

This is how the object will look like:

```swift
// Define a final class for a Writer that inherits from NSManagedObject
final class Writer: NSManagedObject {
    // Define a managed variable for the name of the writer
    @NSManaged var fullName: String?
    // Define a managed variable for the contents created by the writer
    @NSManaged public var contents: NSSet?
}

// Define a class for a Content that inherits from NSManagedObject
class Content: NSManagedObject {
    // Define a managed variable for the publish date of the content
    @NSManaged var publishDate: Date?
    // Define a managed variable for the view count of the content
    @NSManaged var viewCount: Int64
    // Define a managed variable for the writer of the content
    @NSManaged var writer: Writer?
}

// Define a final class for a Blog that inherits from Content
final class Blog: Content {
    // Define a managed variable for the body of the blog
    @NSManaged var body: String?
}
```

We can also do this with a declerative lib: https://github.com/dmytro-anokhin/core-data-model-description

```swift
// Define a CoreDataModelDescription
let dataModelDescription = CoreDataModelDescription(
    entities: [
        // Define an entity for Writer
        .entity(
            name: "Writer",
            managedObjectClass: Writer.self,
            attributes: [
                // Define an attribute for fullName
                .attribute(name: "fullName", type: .stringAttributeType)
            ],
            relationships: [
                // Define a relationship for contents
                .relationship(name: "contents", destination: "Content", toMany: true, deleteRule: .cascadeDeleteRule, inverse: "writer")
            ]),
        // Define an entity for Content
        .entity(
            name: "Content",
            managedObjectClass: Content.self,
            attributes: [
                // Define an attribute for publishDate
                .attribute(name: "publishDate", type: .dateAttributeType),
                // Define an attribute for viewCount
                .attribute(name: "viewCount", type: .integer64AttributeType, isOptional: true)
            ],
            relationships: [
                // Define a relationship for writer
                .relationship(name: "writer", destination: "Writer", toMany: false, inverse: "contents")
            ]),
        // Define an entity for Blog
        .entity(
            name: "Blog",
            managedObjectClass: Blog.self,
            parentEntity: "Content",
            attributes: [
                // Define an attribute for body
                .attribute(name: "body", type: .stringAttributeType)
            ])
    ]
)

// Create the model from the description
let dataModel = dataModelDescription.makeMode()
```

### converting-between-coredata-and-struct

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