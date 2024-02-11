Notes on url in swiftui<!--more-->

### Open URL with openURL environment value

Another way to open a URL is by using the openURL environment value.

To open a URL, you specify a destination URL into an openURL, which is OpenURLAction type.

Here is an example where I manually open a URL using openURL when the button is tapped.
```swift

struct ContentView: View {
    @Environment(\.openURL) private var openURL

    var body: some View {
        Button("Open Website") {
            // 2
            openURL(URL(string: "https://google.com")!)

        }
    }
}
```

Check whether the URL can be open

URL that we can use in openURL isn't limited to website URLs. You can use this to open a custom URL scheme, e.g., open the Google map app (comgooglemaps://).

So, there is a chance that the provided URL isn't valid and the system can't open the specified URL, e.g., the Google Map app doesn't install or a wrong URL scheme.

If you want to know the result of the action, whether it was a success or not, you can specify a completion handler when calling openURL.

In the following example, we open a non-existent URL scheme, sarunw://invalid-url-scheme.
```swift

struct ContentView: View {
    @Environment(\.openURL) private var openURL
    
    var body: some View {
        Button("Open Website") {
            openURL(URL(string: "appName://invalid-url-scheme")!) { canOpen in
                print("Can open \(canOpen)")

            }
        }
    }
}


``` 

### Custom url scheme handler and opener:

Set Custom URL Handler

By default, the system provides a default open URL action (OpenURLAction) that depends on the passing URL.

It has the following behaviors:

Open the associated app for a custom URL scheme or Universal Link.
Open the user's default web browser for a normal URL.
We can opt out of the default behavior by providing a custom handler.

You can do that by setting a new OpenURLAction via environment(_:_:) modifier.

In this example, I am looking for a URL with a google:// scheme and open https://google.com instead.
```swift

struct ExampleApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environment(\.openURL, OpenURLAction(handler: { url in
                    // 1
                    if url.scheme == "google" {

                        return .systemAction(URL(string: "https://google.com")!)
                    }
                    // 2
                    return .systemAction

                }))
        }
    }
}
```
1 If the open URL has a "sarunw" scheme, I return .systemAction(URL(string: "https://google.com")!) which asks the system to open "https://google.com" URL instead.
2 For other URLs, I rely on the default behavior by returning .systemAction.

With this new URL handler, the same code we used in the previous example is now working.

```swift

struct ContentView: View {
    @Environment(\.openURL) private var openURL
    
    var body: some View {
        Button("Open Website") {
            openURL(URL(string: "google://invalid-url-scheme")!) { canOpen in
                print("Can open \(canOpen)")
            }
        }
    }
}
```

google://invalid-url-scheme is now handled by our custom handle, which opens the https://google.com URL instead when tapped.

And since the URL can now be opened, the openURL completion handler now returns true.

