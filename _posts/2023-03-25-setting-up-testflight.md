My notes on testflight<!--more-->

### Why does TestFlight expire?
TestFlight has a general limitation where apps expire after 90 days. This is likely due to Apple's desire to prevent the distribution of apps outside of the App Store.

### The easiest way for a tester to get access to the app is to:
To access the app, testers can follow these steps: 
1. Install TestFlight from the App Store, 
2. Open the app invite email on their device, and 
3. Tap on the highlighted TestFlight word in the email to open TestFlight and install the app.

### Limits:
TestFlight allows you to share your application with up to 10,000 users who can be invited via email or a public link.

### Setup:
To set up beta testing with TestFlight, follow these steps: 
1. Archive your app, 
2. Submit your build to App Store Connect 
3. Add internal and external testers (External == publicly available) 
4. Start beta testing, and 
5. Receive feedback and crash reports.

### Archiving Your App
`⚠️️ By using xCode cloud, you dont have to archive anymore, it does it for you, but could still be useful making things work. Before setting up xCode cloud. ⚠️️`

To distribute your app using TestFlight, follow these steps:

- Open your project in Xcode and navigate to the target editor. Under Signing & Capabilities, ensure that you have a Bundle Identifier and that your Team and Signing Certificate are set correctly. If you're not using your own app, you will need to change the bundle ID to something unique.
- To compile and export your app, select Generic iOS Device in the scheme chooser and create an archive using the `Product ▸ Archive` menu.
- If the build is successful, Xcode will open the Organizer with your app in the Archives tab. Click Distribute App to proceed.
- When prompted to choose your distribution method, select App Store Connect, as this is how you distribute your app when using TestFlight or publishing on the App Store.
- Select Upload as the destination and click Next. The Export option is for saving the signed archive and uploading it later using a different tool.
- Xcode will prepare to submit your app, which includes checking your App Store credentials. App Store will show a prompt with distribution options, and Xcode will select all the checkboxes by default. Leave them selected and click Next.
- The two options shown above are: 1) Include bitcode for iOS content, which allows the App Store to recompile and optimize your code for new devices later, and 2) Upload your app's symbols to receive symbolicated reports from Apple, which uploads debug symbols along with the app so that you get symbolicated crash reports. This is helpful when doing TestFlight testing.
- On the next screen, you can choose automatic signing or manually select your distribution certificate and provisioning profile. Letting Xcode manage your signing makes life easier most of the time. However, if you're managing the signing yourself, select the second option and choose the relevant certificates. When you're ready, click Next.
- Note that you must already have an App Store distribution certificate. If you don't have one, Xcode will prompt you to create one. If prompted, export the signing certificate and store it in a safe place.

### Submit your build to App Store Connect
After Xcode finishes processing your app archive, it presents a summary page for the app you're about to submit. Click Upload to proceed.

- If you receive a '[n]o suitable application records were found' error, this means one of two things: either you didn't create a new app in App Store Connect first, or the bundle identifiers do not match. Make sure you've agreed to all the terms and signed all the contracts within App Store Connect before proceeding.
- Your app will begin uploading to App Store Connect. Xcode displays various messages as it compiles, verifies, and signs your app.
- Once the upload is complete, you can click Done. This completes the work required from Xcode. Your beta build is now available on App Store Connect, where you'll do the rest of the work to set up TestFlight.


### Adding External Testers
- To submit a build for external testing, you need to provide some information in the Test Information section. This includes the 1. Beta App Description, 2. Feedback Email, and 3. Contact Information. Once you've filled in the necessary information, click Save.
- Next, you'll need to add external testers to your testing group. App Store Connect allows you to create multiple testing groups, depending on your needs. For this tutorial, we'll create a group called Top-Testers.
- Instead of adding external testers by email, you can create a public link that allows anyone to install the app. This is useful when you don’t know your testing group ahead of time, such as with a public beta test. To enable the public link, click Enable Public Link under Public Link.
- Note that you'll need to have a build enabled for this group before you can proceed. If the build is not yet reviewed, you'll need to submit it for review and wait for approval.
- App Store Connect will prompt you to confirm that you want to proceed. Once you confirm, you'll receive a TestFlight link that you can share with your testers. The link works the same way as the invitation email: the tester will need to open it on their device, install the TestFlight app if they don't already have it, and then install your app through TestFlight.
- Be aware that testers invited through the link count against your 10,000-tester limit. However, you can set your own limit to reduce the number of people who can access the app through the link. This is useful if you don't want to give away too many free copies of your app.


### Gotchas:
- Testflight apps don't have separate/own sandbox. It'll be treated as regular update (like from one version to another, both from App Store) so all your data will persist. Check out this thread [http://stackoverflow.com/questions/32655508/will-testflight-prerelease-beta-app-version-update-existing-app-store-version-o](http://stackoverflow.com/questions/32655508/will-testflight-prerelease-beta-app-version-update-existing-app-store-version-o) 
- The beta app has exactly the same bundle identifier with the one on the App Store.
- User data (CoreData, Keychain, UserDefault) is pulled based on the **bundle id** of an app. The **bundle id** of an app installed from XCode, TestFlight, or the AppStore may be the same;
- When replacing a testflight app with a release app. There might be a warning saying data will be lost. `You already have this app installed. Do you want to replace the current app version the test version you may lose the app's data` The data wont be lost tho. (this needs confirmation)
- You can't change your bundle ID. The system will (naturally) assume it's a separate app, install it alongside the original, and put it in a brand new sandbox all to itself.
- Apps installed via TestFlight do not auto-update, and will not automatically convert to your App Store version. The user must explicitly download from the App Store. ???
- **Automatic updates**: In TestFlight, automatic updates are turned on by default. Whenever there’s a new build available, it will download automatically and replace the old one. Testers can turn off automatic updates for a particular app in the TestFlight app.
- You can only create public beta-test links if you have **at least one build approved** by Beta App Review.
- If you’re worried that your beta build might get a lot of attention, you can cap the number of testers that can sign up using the public link by clicking Set limit. Once you have enough beta testers, you can also disable a public link altogether by clicking Disable Link.
- ⭐️ when you upload a build to TestFlight it can first be in TestFlight and then go live in the AppStore

### Resources:
- See how long app-store reviews take live: [https://www.runway.team/appreviewtimes](https://www.runway.team/appreviewtimes)  (also server build times etc)

