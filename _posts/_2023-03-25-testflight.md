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
- Open your project in Xcode. In the target editor, under Signing & Capabilities, make sure you have a Bundle Identifier and that your Team and Signing Certificate are set. If you’re not using an app of your own, you will have to change the bundle ID to something unique.
- Now, build and archive the app. This is a way of compiling the app and exporting it in a package that gets uploaded to the App Store. First, choose Generic iOS Device in the scheme chooser. Then, create an archive using the `Product ▸ Archive menu`.
- If everything is OK with the build, Xcode will open the Organizer with your app in the Archives tab. Click Distribute App.
- Next, you’ll choose your distribution method. Select App Store Connect, as this is how you distribute when using TestFlight and when publishing on the app store. Click Next.
- Now, select the destination. Leave Upload selected and click Next. The Export option is for saving the signed archive and uploading it later using a different tool.
- Xcode will prepare to submit your app; this includes checking your App Store credentials. App Store will show a prompt with distribution options, and Xcode will select all the checkboxes by default. Leave them like this and click Next.
- The two options shown above are:
 1. Include bitcode for iOS content: This allows the App Store to recompile and optimize your code for new devices later. It’s rare you’ll need to disable this.
 2. Upload your app’s symbols to receive symbolicated reports from Apple: This uploads debug symbols along with the app so that you get symbolicated crash reports. This is helpful when doing TestFlight testing.
- The next screen asks for distribution signing options. You can choose automatic signing or manually select your distribution certificate and provisioning profile. Letting Xcode manage your signing makes life easier most of the time. But if you’re managing the signing yourself, select the second option along with the relevant certificates. When you’re ready, click Next.
- Note: You must already have an App Store distribution certificate. Otherwise, this prompts you to create one. If prompted, export the signing certificate and store it in a safe place.

### Submit your build to App Store Connect
- Uploading the Archive to the App Store
Once Xcode finishes doing some of its magic, it presents a summary page for the app you’re about to submit. Click Upload.
- Note: If you get a “[n]o suitable application records were found” error, this means one of two things: Either you didn’t first create a new app in App Store Connect, or the bundle identifiers do not match. Make sure you’ve agreed to all the terms and signed all the contracts within App Store Connect.
- Your app will start uploading to App Store Connect. Xcode displays various messages as it compiles, verifies and signs your app.
- You can now smile and click Done. :] That’s all the work required from Xcode. Your beta build is now available on App Store Connect, which is where you’ll do the rest of the work to set up TestFlight.

### Adding External Testers
- First, click Test Information from the menu on the left and fill in the necessary information. This includes:
 1. Beta App Description
 2. Feedback Email
 3. Contact Information
- Providing this information is necessary when submitting a build for external testing. Once completed, click Save.
- Now, click Add External Testers from the menu on the left. App Store Connect asks you to create a new testing group. It’s up to you how you choose to manage your groups. You can have one group for all your testers, different groups for different types of testers or different groups for different apps. For this tutorial, you’ll create one group called Top-Testers.
- **Inviting by Link**: Instead of adding external testers by email, you can create a public link that allows anyone to install the app. This is useful when you don’t know your testing group ahead of time, such as with a public beta test. Under Public Link, click Enable Public Link.
- Note: If you don’t yet have a build enabled for this group, you can add one under the Builds tab above. If the build is not yet reviewed, it will prompt you to submit it for review and you’ll have to wait for approval.
- App Store Connect double-checks that you’re sure you want to proceed. You’ll then get a TestFlight link you can share any way you’d like. This link works exactly the same way the invitation email does: The tester will have to open it on the target device. The page will prompt the user to install the TestFlight app if they don’t already have it, and then it will allow them to install your app through TestFlight.
- In one sense, the link is more convenient than the invitation email. But be careful when sharing it! Testers invited through the link count against your 10,000-tester limit, but you can set your own limit to reduce the number of people who can get the app through the link. This is in case you don’t want to give away 10,000 free copies of your app.

### Gotchas:
- Testflight apps don't have separate/own sandbox. It'll be treated as regular update (like from one version to another, both from App Store) so all your data will persist. Check out this thread http://stackoverflow.com/questions/32655508/will-testflight-prerelease-beta-app-version-update-existing-app-store-version-o
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
- See how long app-store reviews take live: https://www.runway.team/appreviewtimes (also server build times etc)
