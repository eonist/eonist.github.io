My notes on alert for SwiftUI<!--more--> 

- You can store and build custom Alerts with .alert(isPresented: $isShowingUser) { Alert() }
- Buttons are created with Alert.Button and has many presets 
- You add callback to the buttons 

### Gotcha:
- Displaying more than one alert in a view
- If you add multiple alert() modifiers to one view, your code might not work correctly - only one alert will function, the other won't.
- To solve this, connect your alerts to various elements in your view hierarchy, like the button or any other view that causes the alert to show up.

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
struct NotificationDetails: Identifiable {

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
        .alert(item: $details, content: { details in // 5

            Alert(title: Text(details.title),
                  message: Text(details.message))
        })
    }
}
```

### App wide alert controller
Ref: https://sarunw.com/posts/how-to-show-multiple-alerts-on-the-same-view-in-swiftui/
```swift
class AlertManager: ObservableObject {
 
    @Published var details: AlertDetails?

}
@main
struct ExampleApp: App {
 
    @StateObject private var alertManager = AlertManager()
    var body: some Scene {
        WindowGroup {
            MainView()
                .environmentObject(alertManager) 

                .alert(item: $alertManager.details, content: { details in  

                    Alert(title: Text(details.title),
                          message: Text(details.message))
                })
        }
    }
}
struct MainView: View {
 
    @EnvironmentObject var alertManager: AlertManager
    var body: some View {
        VStack {
            Button("Alert A") {
          
                alertManager.details = AlertDetails(

                    id: .first,
                    title: "Title A",
                    message: "Message A")
            }
            Button("Alert B") {
                alertManager.details = AlertDetails(
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
                alertManager.details = AlertDetails(

                    id: .first,
                    title: "Sub Title",
                    message: "Sub Message")
            }
        }
    }
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

### Alert:
- Seems like alert also has a way to pass data into it: https://www.hackingwithswift.com/books/ios-swiftui/using-alert-and-sheet-with-optionals
- More alert options here: https://www.kodeco.com/books/swiftui-by-tutorials/v3.0/chapters/16-sheets-alert-views
- https://sarunw.com/posts/swiftui-dismiss-sheet/