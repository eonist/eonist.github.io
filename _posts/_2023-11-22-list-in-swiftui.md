Notes on list in swiftui<!--more--> 

### ForEach
- A sort of IndexedForEach
- ref: https://stackoverflow.com/a/64262793/5389500
 and with index: https://stackoverflow.com/a/61149111/5389500
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
struct MoviesView: View {
	var body: some View {
		List(TestData.movies, id: \.title) { movie in
			Row(movie: movie)
		}
	}
}


struct MoviesView_Previews: PreviewProvider {
    static var previews: some View {
        MoviesView()
    }
}
struct Row: View {
	let movie: Movie
	
	var body: some View {
		HStack(spacing: 24.0) {
			Image(movie.poster)
				.resizable()
				.frame(width: 70.0, height: 110.0)
				.shadow(color: .gray, radius: 10.0, x: 4.0, y: 4.0)
			VStack(alignment: .leading, spacing: 4.0) {
				Text(movie.title)
					.font(.headline)
				Text(movie.director)
					.font(.subheadline)
				Group {
					Text(movie.genre)
					Text(movie.runtime)
				}
				.font(.caption)
				.foregroundColor(.secondary)
			}
			Spacer()
		}
	}
}

struct Row_Previews: PreviewProvider {
	static var previews: some View {
		Row(movie: TestData.movies[0])
			.padding()
			.previewLayout(.sizeThatFits)
	}
}
```

## adding delete and move:


```swift
struct MoviesView: View {
	@State var movies: [Movie] = TestData.movies
	
	var body: some View {
		List {
			EditButton()
			ForEach (movies, id: \.title) { movie in
				Row(movie: movie)
			}
			.onMove { (source, destination) in
				self.movies.move(fromOffsets: source, toOffset: destination)
			}
			.onDelete { offsets in
				self.movies.remove(atOffsets: offsets)
			}
		}
	}
}
```

First of all, any mutable data in a SwiftUI app needs to be stored in @State and @ObservedObject properties. You can get my free guide on architecting SwiftUI apps with MVC and MVVM to know when to use each.


In SwiftUI, we affect the appearance of views by changing the parameters of initializers and view modifiers.

Whenever a view in SwiftUI needs to update data that resides somewhere else, we use a binding. A binding is a reference that reaches data stored elsewhere, which, in SwiftUI, is called the single source of truth.

`@Binding var movie: Movie` // we pass movie in the init

```swift
Button(action: { self.movie.isFavorite.toggle() }) {
					Heart(isFilled: movie.isFavorite)
						.font(.title)
				}
```

and 

```swift
static let movie = TestData.movies[0]
DetailsView(movie: .constant(movie))
			Group {
				SideInfo(movie: .constant(movie))
				BottomInfo(movie: movie)
			}
// Notice that a $ sign precedes the movie parameter for the SideInfo view. This is a SwiftUI operator you use to connect bindings to data.
SideInfo(movie: $movie)
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
		"Title":"Alita: Battle Angel",
		"Year":"2019",
		"Runtime":"122 min",
		"Genre":"Action, Adventure, Sci-Fi, Thriller",
		"Director":"Robert Rodriguez",
		"Actors":"Rosa Salazar, Christoph Waltz, Jennifer Connelly, Mahershala Ali",
		"Plot":"A deactivated cyborg is revived, but cannot remember anything of her past life and goes on a quest to find out who she is.",
		"Country":"USA",
		"Awards":"8 wins & 25 nominations.",
		"Poster":"Alita"
	}
]
```

```swift
struct Movie: Decodable {
    let title: String
    let year: String
    let runtime: String
    let genre: String
    let director: String
    let actors: String
    let plot: String
    let country: String
    let awards: String
    let poster: String
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

selectable list: https://sarunw.com/posts/swiftui-list-selection/ (List doesn't support rendering and scrolling in a horizontal axis)

SwiftuI's built in List selection functionality doesn't allow for much customization. Here is a way to do it manually:

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
    Image(optionalSystemName: leadingImageName)?
        .iconStyle(size: 18, padding: 0)
    Text(title)
        .rowTextStyle()
    Spacer()
    Text(self.options[0])
        .rowTextStyle()
    Image(systemName: imageName)
        .iconStyle(size: 16, padding: 0)
    }
}
```

### Gotchas
- If all else fails, there is always ccessing uikit from swiftui https://github.com/siteline/swiftui-introspect

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