My notes on alert for SwiftUI<!--more--> 

- You can store and build custom Alerts with .alert(isPresented: $isShowingUser) { Alert() }
- Buttons are created with Alert.Button and has many presets 
- You add callback to the buttons 

### Gotcha:
- Displaying more than one alert in a view
- If you add multiple alert() modifiers to one view, your code might not work correctly - only one alert will function, the other won't.
- To solve this, connect your alerts to various elements in your view hierarchy, like the button or any other view that causes the alert to show up.
- Note on sheets: If you present views as sheets, each sheet needs to implement its own alert, just like MyApp does above. If you have a NavigationView inside your sheet and present other views within this navigation view in the same sheet, the subsequent sheets can use the first sheet's alert.

# Index:
- [Basics](#basics)
- [Simple confirm alert](#simple-confirm-alert)
- [Simple alert with title and message](#simple-alert-with-title-and-message)
- [Simple cancel alert](#simple-cancel-alert)
- [Alternative constructor](#alternative-constructor)
- [Destructive alert](#destructive-alert)
- [Error alert (advanced)](#error-alert-advanced)
- [Multiple alerts](#multiple-alerts)
- [App wide alert controller](#app-wide-alert-controller)
- [TextField and SecureTextField in an alert](#textfield-and-securetextfield-in-an-alert)
- [Additional Alert Resources](#additional-alert-resources)

### Basics:

```swift
struct ContentView: View {
    @State private var presentAlert = false
    var body: some View {
        VStack {
            Text(presentAlert ? "Presenting": "Dismissed")
            Button("Alert") {
                presentAlert = true
            }
            Spacer()
        }
        .padding()
        .alert("Title", isPresented: $presentAlert, actions: {}) 
    }
}
```
### Simple confirm alert
```swift
.alert("Welcome", isPresented: $isShowingUser) {
    Button("OK") { }
}

// Automatically makes OK btn for us

.alert("Welcome", isPresented: $isShowingUser) { }
```

### Simple alert with title and msg: 
```swift
.alert("Title", isPresented: $presentAlert, actions: {
    // actions
}, message: {
    Text("Message")
})
```

### Simple cancel alert:
```swift

// iOS 15
.alert("Title", isPresented: $presentAlert, actions: {

    Button("Cancel", role: .cancel, action: {})

}, message: {
 
    Text("Message")

})
```

### Alternative constructor:
```swift
.alert(isPresented: $presentAlert) { // 2
    let okBtn = Alert.Button.destructive(Text("Clear all data")) {
    prefrencesSheetHandler.isShowingSheet = false // deactivate the alert
    }
    let cancelBtn = Alert.Button.cancel( Text("Cancel")) {
    prefrencesSheetHandler.isShowingSheet = false // deactivate the alert
    }
    return Alert.init(title: Text("Confirmation needed"),
                    message: Text("Are you sure you want to remove this device?"),
                    primaryButton: okBtn, secondaryButton: cancelBtn)
}
```

### Destructive alert: 
```swift
.alert("Title", isPresented: $presentAlert, actions: {
    Button("Destructive", role: .destructive, action: {})
}, message: {
    Text("Message")
})
``` 

### Error alert: (advance)

```swift
// Define a struct for error details
struct ErrorDetails: LocalizedError {
    var description: String?
    var reason: String?
    var suggestion: String?
    var help: String?
}

// Define the main view
struct MainView: View {
    // State variables for alert presentation and error details
    @State private var showAlert = false
    @State private var errorDetails: ErrorDetails?

    var body: some View {
        VStack {
            // Display the alert status
            Text(showAlert ? "Presenting": "Dismissed")
            // Display the error description
            Text(errorDetails?.description ?? "nil")
            // Button to trigger the alert
            Button("Trigger Alert") {
                // Set the error details
                errorDetails = ErrorDetails(
                    description: "Error Description",
                    reason: "Failure Reason",
                    suggestion: "Try Again",
                    help: "Help Anchor"
                )
                // Set the alert status to true
                showAlert = true
            }
            Spacer()
        }
        .padding()
        // Alert presentation
        .alert(
            isPresented: $showAlert,
            error: errorDetails, // Pass the error details
            actions: { error in // Define the alert actions
                if let suggestion = error.suggestion {
                    Button(suggestion, action: {
                        // Recovery action
                    })
                }
            }, message: { error in // Define the alert message
            if let reason = error.reason {
                Text(reason)
            } else {
                Text("Something went wrong")
            }
        })
    }
}
```

### Multiple alerts:
```swift
struct NotificationDetails: Identifiable { // identifiable is key to using diffent alerts etc

    enum NotificationType {
        case first
        case second
    }

    let id: NotificationType
    let title: String
    let message: String
}

struct MainView: View {
    @State private var details: NotificationDetails?
    var body: some View {
        VStack {
            Button("Notification 1") {
                details = NotificationDetails(
                    id: .first,
                    title: "Title 1",
                    message: "Message 1")
            }
            Button("Notification 2") {
                details = NotificationDetails(
                    id: .second,
                    title: "Title 2",
                    message: "Message 2")
            }
        }
        .alert(item: $details, content: { details in // use the speccific alert 
            Alert(title: Text(details.title),
                  message: Text(details.message))
        })
    }
}
```

### App wide alert controller
- Ref: https://sarunw.com/posts/how-to-show-multiple-alerts-on-the-same-view-in-swiftui/
- Similar but using bool to toggle alert (and simpler environment key etc): https://stackoverflow.com/a/69301872/5389500
- a bit different environment key etc: https://stackoverflow.com/a/67568887/5389500
- And similar again: https://dylancfe15.medium.com/swiftui-how-noobs-and-pros-implement-alerts-bf17188113fd
-  ⚠️️ A drawback is that it wont work in preview, if the alert is inited in the App struct
```swift
// A struct that conforms to Identifiable protocol. This use as a trigger for our alert presentation.
struct AlertDetails: Identifiable {
    enum AlertType {
        case first
        case second
    }
    let id: AlertType
    let title: String
    let message: String
}
class AlertManager: ObservableObject {
    // After setting details to a non-nil value, the alert will be present with our info as a parameter of a content closure 
    @Published var details: AlertDetails?
}
@main
struct ExampleApp: App {
 
    @StateObject private var alertManager = AlertManager()
    var body: some Scene {
        WindowGroup {
            MainView()
                .environmentObject(alertManager) // we attach the alertManager to a global environmentObject here
                .alert(item: $alertManager.details, content: { details in  // item is the model
                    Alert(title: Text(details.title),
                          message: Text(details.message))
                })
        }
    }
}
struct MainView: View {
    @EnvironmentObject var alertManager: AlertManager // access to the global enironment var 
    var body: some View {
        VStack {
            Button("Alert A") {
                alertManager.details = AlertDetails( // trigger showing the alert
                    id: .first,
                    title: "Title A",
                    message: "Message A")
            }
            Button("Alert B") {
                alertManager.details = AlertDetails( // trigger showing the alert
                    id: .second,
                    title: "Title B",
                    message: "Message B")
            }
            SubView()
        }
    }
}
struct SubView: View {
    @EnvironmentObject var alertManager: AlertManager
    var body: some View {
        VStack {
            Button("Sub Alert") {
                alertManager.details = AlertDetails( // trigger showing the alert

                    id: .first,
                    title: "Sub Title",
                    message: "Sub Message")
            }
        }
    }
}
```

### Simple top level alert: 
Here is a possible example solution to show an Alert anywhere in the App. It uses "Environment" and "ObservableObject".

```swift
// create the Alerter class that notifies the top-level and asks to display an alert
class Alerter: ObservableObject {
   @Published var alert: Alert? {
      didSet { isShowingAlert = alert != nil }
   }
   @Published var isShowingAlert = false
}
// render the alert at the top-most level, for example in your @main struct or the ContentView
                                             @main
                                             struct MyApp: App {
   @StateObject var alerter: Alerter = Alerter()
   
   var body: some Scene {
      WindowGroup {
         ContentView()
            .environmentObject(alerter)
            .alert(isPresented: $alerter.isShowingAlert) {
               alerter.alert ?? Alert(title: Text(""))
            }
      }
   }
}
//set the alert that should be displayed from inside a child view
struct SomeChildView: View {
   
   @EnvironmentObject var alerter: Alerter
   
   var body: some View {
      Button("show alert") {
         alerter.alert = Alert(title: Text("Hello from SomeChildView!"))
      }
   }
}
```

### App wide alert that uses root controller and UIKit (works in swiftui)

```swift
import SwiftUI
/**
 * - Note: Ref: https://makestory.medium.com/how-to-present-alert-from-anywhere-in-swiftui-754a89da3321
 * ## Example:
 * presentAlert(title: "Save", subTitle: "Saving is required", primaryAction: .init(title: "OK", style: .default) { _ in
 *     print("Handler")
 * })
 *
 * And
 *
 * Button( action:{
 *    presentAlert(title: "Title", subTitle: "subTitle",
 *                 primaryAction: .init(title: "Primary", style: .default, handler: {_ in
 *       print("Handler")
 *    }))
 * } ) {
 *    Text("Present Alert")
 *    }
 *
 * And
 *
 * Button( action:{
 *    presentAlert(title: "Title", subTitle: "subTitle",
 *                 primaryAction: .init(title: "Primary", style: .default, handler: {_ in
 *       print("Handler")
 *    }),secondaryAction: .init(title: "Second", style: .cancel, handler: {_ in
 *       print("Handler2")
 *    }))
 * } ) {
 *    Text("Present Alert2")
 *    }
 */
func presentAlert(title: String, subTitle: String, primaryAction: UIAlertAction, secondaryAction: UIAlertAction? = nil) {
   DispatchQueue.main.async {
      let alertController = UIAlertController(title: title, message: subTitle, preferredStyle: .alert)
      alertController.addAction(primaryAction)
      if let secondary = secondaryAction {
         alertController.addAction(secondary)
      }
      rootController?.present(alertController, animated: true, completion: nil)
   }
}
/**
 * rootController
 */
fileprivate var rootController: UIViewController? {
   let keyWin = UIApplication.shared.connectedScenes.compactMap { $0 as? UIWindowScene } .flatMap { $0.windows } .first { $0.isKeyWindow }
   var root = keyWin?.rootViewController
   while let presentedViewController = root?.presentedViewController {
      root = presentedViewController
   }
   return root
}

```

### TextField and SecureTextField in an alert:
ref: https://sarunw.com/posts/swiftui-alert-textfield/
```swift
struct MainView: View {
    @State private var showAlert = false
    @State private var userId: String = ""
    @State private var userPassword: String = ""
    
    var body: some View {
        Button("Display Alert") {
            showAlert = true            
        }
        .alert("Sign In", isPresented: $showAlert, actions: {
            TextField("User ID", text: $userId)

            SecureField("User Password", text: $userPassword)

            // Button to perform login action
            Button("Sign In", action: {})
            // Button to cancel the login action
            Button("Abort", role: .cancel, action: {})
        }, message: {
            Text("Kindly input your user ID and password.")
        })
    }
}
```

### Resources:
- Seems like alert also has a way to pass data into it: https://www.hackingwithswift.com/books/ios-swiftui/using-alert-and-sheet-with-optionals
- More alert options here: https://www.kodeco.com/books/swiftui-by-tutorials/v3.0/chapters/16-sheets-alert-views
- https://sarunw.com/posts/swiftui-dismiss-sheet/
- Nice writeup on various modals, alerts etc https://skyspiritlabs.com/displaying-ios-alerts-using-uialertcontroller-and-swiftui/
- A workaround for multiple alert issues (using emptyview and overlay): https://stackoverflow.com/a/72370615/5389500