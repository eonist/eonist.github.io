My notes on sheets in swiftUI<!--more-->

### Simple sheet example: 

```swift
// This view is displayed as a full screen modal
struct FullScreenPopupView: View {
    // Environment variable to dismiss the modal
    @Environment(\.dismiss) var close

    var body: some View {
        ZStack {
            // Fill the entire screen with a primary color
            Color.primary.edgesIgnoringSafeArea(.all)
            // Button to dismiss the modal
            Button("Close Popup") {
                close()
            }
        }
    }
}

// This is the main content view
struct MainView: View {
    // State variable to control the presentation of the modal
    @State private var showModal = false

    var body: some View {
        // Button to present the modal
        Button("Show!") {
            showModal.toggle()
        }
        // Present the modal when the state variable is true
        .fullScreenCover(isPresented: $showModal, content: FullScreenPopupView.init)
    }
}
```

### Controlling sheet from a subview:

```swift
import SwiftUI

class Model: ObservableObject {
    @Published var show = false
}
struct SubView: View {
    @EnvironmentObject var model: Model
    var tag: Int
    var body: some View {
        VStack {
            NavigationLink(destination: SubView(tag: tag + 1).environmentObject(model)) {
                Text("subview \(tag)")
            }
            if tag == 2 {
                Toggle(isOn: $model.show) {// toggle sheet
                    Text("toggle")
                }.padding()
            }
        }.navigationBarTitle("subview \(tag)")
    }
}
struct ContentView: View {
    @ObservedObject var model = Model()
    var body: some View {
        NavigationView {
            SubView(tag: 0).environmentObject(model) // inject sheet binding
        }.sheet(isPresented: $model.show) {
            Text("sheet")
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

### Presenting as sheet or fullCover (backward compatibility):
Ref: https://stackoverflow.com/a/63056850/5389500

```swift
struct DemoCompatibleFullScreen: View {
    @State private var activateFullScreen = false
    var body: some View {
        Button("Toggle") { self.activateFullScreen.toggle() }
            .compatibleFullScreen(isPresented: $activateFullScreen) {
                Text("I'm in Full Screen!")
            }
    }
}

extension View {
    func compatibleFullScreen<Content: View>(isPresented: Binding<Bool>, @ViewBuilder content: @escaping () -> Content) -> some View {
        self.modifier(FullScreenModifier(isPresented: isPresented, builder: content))
    }
}

struct FullScreenModifier<V: View>: ViewModifier {
    let isPresented: Binding<Bool>
    let builder: () -> V

    @ViewBuilder
    func body(content: Content) -> some View {
        if #available(iOS 14.0, *) {
            content.fullScreenCover(isPresented: isPresented, content: builder)
        } else {
            content.sheet(isPresented: isPresented, content: builder)
        }
    }
}
```

### Resources:
- it's also possible to pass item in the sheet (seems this doesnt need isShowingSheet etc) see:  https://medium.com/@ganeshrajugalla/swiftui-how-to-use-sheets-sheet-38dc2d22b1d3
- Customizing sheet in SwiftUI: https://github.com/edudnyk/SheeKit
- More nuances on sheets in SwiftUI; https://www.swiftyplace.com/blog/swiftui-sheets-modals-bottom-sheets-fullscreen-presentation-in-ios
-  here is a way to customize sheet popover: https://rudrank.blog/custom-modal-ipad-swiftui
- hacky custom popover for legacy swiftui: https://github.com/piterwilson/SwiftUI-Modal-on-iPad

