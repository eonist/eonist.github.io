Notes on list in SwiftUI<!--more-->

### ForEach
- A sort of "IndexedForEach" that works similarly to how a ViewModifier works, but for multiple items
- Ref: https://stackoverflow.com/a/64262793/5389500
- And with index: https://stackoverflow.com/a/61149111/5389500

```swift
struct EnumeratedForEach<ItemType, ContentView: View>: View { 
    let data: [ItemType]
    let content: (Int, ItemType) -> ContentView
    init(_ data: [ItemType], @ViewBuilder content: @escaping (Int, ItemType) -> ContentView) {
        self.data = data
        self.content = content
    }
    var body: some View {
        ForEach(Array(zip(data.indices, data)), id: \.0) { idx, item in
            content(idx, item)
        }
    }
}
// Now you can use it like this:
EnumeratedForEach(items) { idx, item in
    ...
}
```

### Creating table: 
If you come from UIKit, you will appreciate how easy it is to create tables in SwiftUI. Gone are the days of cell prototypes, data sources, and delegates.

Now, all you need to do is create a List view, pass it an array with your data and declare which views to use as rows.



```swift
struct FilmListView: View {
    var body: some View {
        List(TestData.films, id: \.title) { film in
            FilmRow(film: film)
        }
    }
}

struct FilmListView_Previews: PreviewProvider {
    static var previews: some View {
        FilmListView()
    }
}

struct FilmRow: View {
    let film: Film
    
    var body: some View {
        HStack(spacing: 24.0) {
            Image(film.poster)
                .resizable()
                .frame(width: 70.0, height: 110.0)
                .shadow(color: .gray, radius: 10.0, x: 4.0, y: 4.0)
            VStack(alignment: .leading, spacing: 4.0) {
                Text(film.title)
                    .font(.headline)
                Text(film.director)
                    .font(.subheadline)
                Group {
                    Text(film.genre)
                    Text(film.runtime)
                }
                .font(.caption)
                .foregroundColor(.secondary)
            }
            Spacer()
        }
    }
}

struct FilmRow_Previews: PreviewProvider {
    static var previews: some View {
        FilmRow(film: TestData.films[0])
            .padding()
            .previewLayout(.sizeThatFits)
    }
}
```

## Adding delete and move:

```swift
struct FilmsView: View {
    @State var films: [Film] = TestData.films
    
    var body: some View {
        List {
            EditButton()
            ForEach (films, id: \.title) { film in
                FilmRow(film: film)
            }
            .onMove { (source, destination) in
                self.films.move(fromOffsets: source, toOffset: destination)
            }
            .onDelete { offsets in
                self.films.remove(atOffsets: offsets)
            }
        }
    }
}
```

First of all, any mutable data in a SwiftUI app needs to be stored in @State and @ObservedObject properties. You can get my free guide on architecting SwiftUI apps with MVC and MVVM to know when to use each.

In SwiftUI, we affect the appearance of views by changing the parameters of initializers and view modifiers.

Whenever a view in SwiftUI needs to update data that resides somewhere else, we use a binding. A binding is a reference that reaches data stored elsewhere, which, in SwiftUI, is called the single source of truth.

`@Binding var film: Film` // we pass movie in the init

```swift
Button(action: { self.film.isLiked.toggle() }) {
    LikeIcon(isFilled: film.isLiked)
        .font(.title)
}
```

and 

```swift
static let film = TestData.films[0]
DetailsView(film: .constant(film))
            Group {
                SideDetails(film: .constant(film))
                BottomDetails(film: film)
            }
// Notice that a $ sign precedes the film parameter for the SideDetails view. This is a SwiftUI operator you use to connect bindings to data.
SideDetails(film: $film)
```

You can connect bindings to @State and @ObservedObject properties, or other bindings, like in this case. These chains of bindings allow us to pass changes up the view hierarchy until we reach the single source of truth. To know more

While the main navigation of an iOS app is managed by architectural views like NavigationView and TabView, modal presentation happens through three view modifiers:

the .alert modifier shows a small alert panel in the middle of the screen, with one or two buttons;
the .actionSheet modifier presents a menu with several options;
the .sheet modifier presents a full-screen view.


### Examples:

```json
[
	{
        "FilmName":"Alita: Battle Angel",
        "ReleaseYear":"2019",
        "Duration":"122 min",
        "Category":"Action, Adventure, Sci-Fi, Thriller",
        "Filmmaker":"Robert Rodriguez",
        "Performers":"Rosa Salazar, Christoph Waltz, Jennifer Connelly, Mahershala Ali",
        "Storyline":"A deactivated cyborg is revived, but cannot remember anything of her past life and goes on a quest to find out who she is.",
        "Origin":"USA",
        "Accolades":"8 wins & 25 nominations.",
        "CoverImage":"Alita"
	}
]
```

```swift
struct Film: Decodable {
    let name: String
    let releaseYear: String
    let duration: String
    let category: String
    let filmmaker: String
    let performers: String
    let storyline: String
    let origin: String
    let accolades: String
    let coverImage: String
}
```

```swift
struct TestData {
	static var movies: [Movie] = {
		let url = Bundle.main.url(forResource: "Movies", withExtension: "json")!
		let data = try! Data(contentsOf: url)
		let decoder = JSONDecoder()
		return try! decoder.decode([Movie].self, from: data)
	}()
}
```


preview cells:

```swift
struct DecisionCell_Previews: PreviewProvider {
   static var previews: some View {
       DecisionCell(
           decision: Decision(handValue: 6, dealerCardValue: .nine, action: .hit), myHandValue: 8, dealerCardValue: .five)
           .previewLayout(.sizeThatFits)
           .padding()
   }
}
```
_PreviewOrientation returns a Group containing the original View in portrait and landscape modes:
```swift
struct _PreviewOrientation<Value: View>: View {
   private let viewToPreview: Value

   init(_ viewToPreview: Value) {
       self.viewToPreview = viewToPreview
   }

   var body: some View {
       Group {
           viewToPreview
           viewToPreview.previewInterfaceOrientation(.landscapeRight)
       }
   }
}
```
To use this view builder in a preview, simply pass in the View you're previewing:
```swift
struct ContentView_Previews: PreviewProvider {
   static var previews: some View {
       _PreviewOrientation(
           ContentView()
       )
   }
}
```

## Selectable list

- selectable list: https://sarunw.com/posts/swiftui-list-selection/ (List doesn't support rendering and scrolling in a horizontal axis)

- SwiftUI's built in List selection functionality doesn't allow for much customization. Here is a way to do it manually:

1. Create a state that is an optional int
2. Create a list where id is the int. `List(Array(items.indecies), id: \.self)`
3. Create items: `{ i in let item = items[i] }`
3. Set the state on tap of the list item
4. Change the selection state by doing: `let isSelected = selectedIndex == i`
5. Change selected state in the item: `let row = Row(isSelected: isSelected)`


## Native picker button:
There is also detail view as picker: https://swiftwithmajid.com/2019/06/19/building-forms-with-swiftui/ and 4 other picker styles here: https://sarunw.com/posts/swiftui-form-picker-styles/
```swift
struct ContentView: View {
    @State private var selectedItem: Int = 0
    
    var body: some View {
        VStack {
            Text("Content")
            // 1
            Picker("Color", selection: $selectedItem) {
                Text("Red").tag(0)
                Text("Blue").tag(1)
                Text("Green").tag(2)
            }
        }
        .padding()
        .toolbar {
            ToolbarItem {
                // 2
                Picker("Color", selection: $selectedItem) {
                    Text("Red").tag(0)
                    Text("Blue").tag(1)
                    Text("Green").tag(2)
                }
            }
        }
    }
}
```
## Creating a picker button manually: 

```swift
Menu {
    ForEach(options, id: \.self) { text in
        Button(text) { Swift.print("action!"); action() }  // Label("Add", systemImage: "plus.circle")
    }
} label: {
    HStack {
        Image(optionalSystemName: leadingImageName)? // left
            .iconStyle(size: 18, padding: 0)
        Text(title)
            .rowTextStyle()
        Spacer()
        Text(self.options[0]) // right
            .rowTextStyle()
        Image(systemName: imageName)
            .iconStyle(size: 16, padding: 0)
    }
}
```

### Gotchas
- If all else fails, there is always accessing uikit from swiftui https://github.com/siteline/swiftui-introspect

### Resources:
- dynamic list: https://sarunw.com/posts/swiftui-dynamic-list/
- foreach vs list: https://sarunw.com/posts/difference-between-list-foreach/
- scrollview: https://sarunw.com/posts/how-to-use-scrollview-in-swiftui/
- nuanced info on scrolling: https://itnext.io/deep-dive-into-the-new-features-of-scrollview-in-swiftui-5-440b9f0e0e09
- scroll to start and end: https://www.appcoda.com/scrollview-scroll-positions/
- scroll position detection: https://saeedrz.medium.com/detect-scroll-position-in-swiftui-3d6e0d81fc6b
- using scrollviewreader: https://www.hackingwithswift.com/quick-start/swiftui/how-to-make-a-scroll-view-move-to-a-location-using-scrollviewreader and https://developer.apple.com/documentation/swiftui/scrollviewreader and https://doordash.engineering/2022/07/21/programmatic-scrolling-with-swiftui-scrollview/ and https://swiftspeedy.com/scroll-to-the-bottom-or-top-programmatically-in-swiftui/ and https://blog.devgenius.io/swiftui-tutorial-programmatic-scrolling-with-scrollviewreader-5950b2f97765
- advance scrolling in swiftui via uikit: https://doordash.engineering/2022/07/21/programmatic-scrolling-with-swiftui-scrollview/
- tracking current scroll item: https://stackoverflow.com/questions/75617096/scrollviewreader-how-to-read-the-current-row
- Nuanced scrollview info: https://www.swiftyplace.com/blog/how-to-use-swiftui-scrollview
- observing content offset: https://www.swiftbysundell.com/articles/observing-swiftui-scrollview-content-offset/ and https://github.com/maxnatchanon/trackable-scroll-view
- has custom snapping for hstack: https://levelup.gitconnected.com/snap-to-item-scrolling-debccdcbb22f
- Get the current position of ScrollView in SwiftUI https://stackoverflow.com/questions/56503243/get-the-current-position-of-scrollview-in-swiftui
- pull to refresh: https://sarunw.com/posts/pull-to-refresh-in-swiftui/
- pagination: https://github.com/crelies/AdvancedList
- styling: https://www.hackingwithswift.com/quick-start/swiftui/how-to-create-grouped-and-inset-grouped-lists
- oldschool UIKit wheel picker: https://stackoverflow.com/questions/75775079/how-to-put-picker-into-context-menu-popup-in-swiftui