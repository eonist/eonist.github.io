My notes on adding apps to Apple app store<!--more-->

⚠️️ Important: https://developer.apple.com/app-store/review/#common-app-rejections  
⚠️️ Important: https://github.com/aashishtamsya/Appstore-Review-Guidelines  
⚠️️ Important: https://github.com/whitef0x0/app-store-checklist  
⚠️️ Important: https://www.raywenderlich.com/2625-introduction-to-the-testflight-sdk

### Here’s how to submit your app to the Apple App Store:
Great tutorial for submitting to the Appstore: https://medium.com/macoclock/how-to-submit-ios-app-to-app-store-c1e801ff817b

1. To create an iOS distribution provisioning profile and distribution certificate, follow the instructions provided by Apple's documentation.
2. Create an App Store Connect record for your app by adding a new app.
3. To archive and upload your app using Xcode, select 'Generic iOS Device' from the list of simulators, then click 'Product' -> 'Archive'. Finally, click 'Distribute App' to upload your app.
4. Configure your app's metadata and further details in its App Store Connect record.
5. Submit your app for review.
6. Check on the status of your app.

### Testflight
Great tutorial for adding an app to test-flight: https://reinteractive.com/posts/344-how-to-upload-your-ios-app-to-testflight-for-distribution-beta

1. The first build of your app must be approved by TestFlight App Review (This build is automatically sent for review when you add it to a group.)
2. To upload a beta build to App Store Connect, select the build you want to upload and click 'Add to TestFlight'. Builds remain active for 90 days after upload.
3. Internal testers (100 org members) and external testers (10k people) can be invited via email or by enabling and sharing a public link. To enable a public link, go to your app's TestFlight page, click an existing group, and click 'Enable Public Link'.
4. When sharing your app with external testers, provide details on your app's Test Information page in App Store Connect.

### App review:
- Read this: https://developer.apple.com/app-store/review/guidelines/
- There are two review processes. One for app store, and one for TestFlight.
- It's not intuitive, but you have to add app builds to the app store review page to be able to initiate TestFlight app review. So you can begin testing on TestFlight before passing app store review. The TestFlight review is supposed to be less strict than app review (which can be strict, especially passing the first review).
- https://www.revenuecat.com/blog/getting-through-app-review

### Find message that resonates with target user:
- Identify the core user problem or pain.
- Present an innovative solution that solves the problem.

### Organic search
- Keywords, metadata, app name, and description.

#### Title (App name)
Your app’s name plays a critical role in how users discover it on the App Store. Choose a simple, memorable name that is easy to spell and hints at what your app does. Be distinctive. Avoid names that use generic terms or are too similar to existing app names. An app name can be up to **30 characters long**.

#### Subtitle
- Your app’s subtitle is intended to summarize your app in a concise phrase. Consider using this, rather than your app’s name, to explain the value of your app in greater detail.
- Avoid generic descriptions such as “world’s best app.” Instead, highlight features or typical uses of your app that resonate with your audience.
- You can update your subtitle when submitting a new version of your app to help you determine the subtitle that’s most effective for engaging users.
- A subtitle can be up to 30 characters long and appears below your app’s name throughout the App Store.

#### Description
- Craft an engaging description that highlights the features and functionality of your app. The ideal description is a concise, informative paragraph followed by a short list of main features. Let potential users know what makes your app unique and why they'll love it. Communicate in the tone of your brand, and use terminology your target audience will appreciate and understand.
- The first sentence of your description is the most important, as it's what users can read without having to tap to read more. Every word counts, so focus on your app's unique features.
- If you choose to mention an accolade, we recommend putting it at the end of your description or as part of your promotional text. Don't add unnecessary keywords to your description in an attempt to improve search results. Also, avoid including specific prices in your app description, as pricing is already shown on the product page and references within the description may not be accurate in all regions.
- You can update your app's description when you submit a new version of your app. If you want to share important updates more frequently, consider using your promotional text instead.
- Focus the bulk of your energy on the first three lines of your description to immediately grab your reader's attention. Given the ever-growing number of apps in the marketplace, customers are sure to have a few — if not several — alternatives to consider when evaluating yours. Make their decision easy by immediately communicating what it does and why they should use it.


### Promotional text
Your app's promotional text appears at the top of the description and is up to 170 characters long. You can update promotional text at any time without having to submit a new version of your app. Consider using this to share the latest news about your app, such as limited-time sales or upcoming features.

#### Screenshots
- Use screenshots captured from your app's UI to visually communicate your app's user experience. You can feature up to 10 screenshots on your App Store and Mac App Store product pages. Depending on the orientation of your screenshots, the first one to three images will appear in search results when no app preview is available. Make sure these highlight the essence of your app. Focus each subsequent screenshot on a main benefit or feature so that you fully convey your app's value. If your app supports Dark Mode, consider including at least one screenshot that showcases what the experience looks like for users.
- For more information on uploading screenshots, see the [App Store Connect Help](https://help.apple.com/app-store-connect/#/devd274dd925).
- You have to upload 2 sizes of screenshots. launch simulator for **iPhone6 and iPhoneX** and click screenshot button to grab some shots
 1. For **6.5 inches iPhones** (1242 x 2688px or 1284 x 2778px)
 2. For **iPhone 5.5" Display** the trick is to take a shot and then resize in preview to 1242 x 2208 (the extra pixel must be removed to the height)
- For mac:  `1280x800` `1440x900` `2560x1600` `2880x1800`
- iPads (2048 x 2732px)

#### Keywords
- Keywords help determine where your app displays in search results, so choose them carefully to ensure your app is easily discoverable. Choose keywords based on words you think your audience will use to find an app like yours. Be specific when describing your app’s features and functionality to help the search algorithm surface your app in relevant searches. Consider the trade-off between ranking well for less common terms versus ranking lower for popular terms. Popular, functional terms, such as “jobs” or “social”, may drive a lot of traffic, but are highly competitive in the rankings. Less common terms drive lower traffic, but are less competitive.
- Keywords are limited to 100 characters total, with terms separated by commas and no spaces. (Note that you can use spaces to separate words within keyword phrases. For example: Property,House,Real Estate.) Maximize the number of words that fit in this character limit by avoiding the following:
- Plurals of words that you’ve already included in singular form
- Names of categories or the word “app”
- Duplicate words
- Special characters — such as # or @ — unless they’re part of your brand identity. Special characters don’t carry extra weight when users search for your app.
- Improper use of keywords is a common reason for App Store rejections. Do not use the following in your keywords:
- Unauthorized use of trademarked terms, celebrity names, and other protected words and phrases
- Terms that are not relevant to the app
- Competing app names
- Irrelevant, inappropriate, offensive, or objectionable terms
- In addition, keep in mind that promotional text doesn’t affect your app’s search ranking so it should not be used to display keywords.


#### What’s New
- When you update your app, you can use What’s New to communicate changes to users. This text appears on your product page and on the Updates tab.
- If you added a feature or fixed a bug based on feedback, use What’s New to let users know that you’ve listened to them. List new features, content, or functionality in order of importance, and add call-to-action messaging that gets users excited about the update.
- Update frequently: Mobile customers are looking for apps that are constantly improving, with regular updates based on customer feedback. Apps that are frequently updated are seen, by both the app store and the customer, to be of a higher value and more customer-centric.

#### Ratings and reviews
- Ratings and reviews influence how your app ranks in search and can encourage users to engage with your app from search results, so focus on providing a great app experience that motivates users to leave positive reviews. Individual ratings inform your app’s summary rating, which is displayed on your product page and in search results. This summary rating is specific to each territory on the App Store.
- The `SKStoreReviewController` API lets you give users an easy way to provide feedback about your app. You can prompt for ratings up to three times in a 365-day period. Users will submit a rating through the standardized prompt, and can write and submit a review without leaving the app.
- You can use App Store Connect to **respond to customer reviews of your app** to directly address their feedback, questions, and concerns. When you respond, the reviewer will be notified and will have the option to update their review. Reviews and responses can be updated at any time, but only the latest review and response for each user will display on your product page.

#### Categories
Categories on the App Store help users discover new apps to meet their needs. You can assign a primary and a secondary category to your app. The primary category is particularly important for discoverability, as it helps users find your app when browsing or filtering search results, and it determines in which tab your app appears on the App Store. Be sure to select the primary category that’s most relevant. **Choosing categories that are not relevant to your app** may cause your app to be rejected when submitted for review.

#### Gotchas:
- `Bundle-ID` – here you can select from the app identifiers that are registered in the Developer Account (this value is unique for every app on the App Store). If the app build is ready, make sure you choose the same ID that was used in the Xcode during app archiving.
- ⚠️️ Important ⚠️️ When you create a new build, **you have to increment the build nr in general - build nr**
- You can specify the app price for a specific time period. It is useful if you want to make your app cheaper at the beginning to encourage users to download it
- sku nr: for profit record keeping. you can use `app-id`, or date or version. `NameOfAppV1` y2022m12d04
- Click save button a lot when filling out apples app store info pages (it's a lot to fill out can take a day to finish 😅)
- Apple app review is a bit random, you might get rejected for wrong reasons etc. So make sure you have all the obvious things in check. Like the design and UX works as expected **with-out bugs etc**. or else the review will be a lot of back and forth
- Send apps for review early and often review times can be found here: https://www.runway.team/appreviewtimes
- If rejected, send succinct info to reviewer. and try again. if it doesn't resolve. appeal to higher ups with your claim etc.
- Reviewers Are usually not responsive and wont go out of their way to understand. So make things simple and keep trying. Also reviewers might change. So dont expect them to know anything regarding previous information.
- It may take a little time after a build is uploaded to app connect, before it shows up in the app connect dashboard.
- After the build is added to the app store build dashboard. it will take some time before it can be added to TestFlight. It will say processing for a while. And then after a while it will be ready for testflight review. For current time see: https://www.runway.team/appreviewtimes
- Sku nr: use bundle id: https://stackoverflow.com/a/8710967/5389500

### FAQ:
- Q: Do I need to get approval to start testing beta builds?
- A: To make your beta builds available to members of your App Store Connect team, your app doesn’t need review by TestFlight App Review. To make your beta builds **available to external users**, your beta build may require review. When you add the first build of your app to a group, the build gets sent to TestFlight App Review to make sure it follows the App Store Review Guidelines. A review is only required for the first build of a version and subsequent builds may not need a full review. Testing can begin once a build is approved.

### Tips:
- The fastest and easiest way to make screenshots is just to launch your app in the iOS simulator (for the screen size that you need), navigate to the screen you want to take a screenshot of and then hit `CMD + S` on your keyboard.
- Use [freeprivacypolicy.com](https://www.freeprivacypolicy.com)  to generate privacy policy. Store in a html on your website or in a github gist (more info: https://developer.apple.com/app-store/review/guidelines/#privacy) (or google for “App Privacy Policy” and you’ll see a lot of samples and privacy policy generators that you can use)

### Resources:
- **Review guidelines:** [https://developer.apple.com/app-store/review/guidelines](https://developer.apple.com/app-store/review/guidelines)
- Bezels and badges: [https://developer.apple.com/app-store/marketing/guidelines/](https://developer.apple.com/app-store/marketing/guidelines/)
- **App store submission checklist**: [https://clearbridgemobile.com/how-to-submit-an-app-to-the-app-store/](https://clearbridgemobile.com/how-to-submit-an-app-to-the-app-store/)
- Another checklist: https://www.thedroidsonroids.com/blog/how-to-submit-app-to-the-app-store-guide-and-checklist
- In app purchase: https://developer.apple.com/in-app-purchase/
- 100 promo codes: https://help.apple.com/app-store-connect/#/dev50869de4a
- Promoted in-app purchases: https://developer.apple.com/app-store/promoting-in-app-purchases/
- Overview off appstore functionality https://developer.apple.com/app-store-connect/api/
- Testflight tutorial: https://reinteractive.com/posts/344-how-to-upload-your-ios-app-to-testflight-for-distribution-beta
- Add app to appstore (a bit outdated) https://reinteractive.com/posts/344-how-to-upload-your-ios-app-to-testflight-for-distribution-beta
- Bundle apps from different platforms in one bundle purchase https://developer.apple.com/support/universal-purchase/
- For mockups: https://mockuphone.com/
- ⭐ 10 step tutorial to publish an app: https://medium.com/@taraparakj75/https-bosctechlabs-com-steps-for-ios-developers-submit-app-to-app-store-e068dbb1f3ab
- Apples official appstore submit tutorial: https://developer.apple.com/ios/submit/
- Mac testflight walkthrough: https://developer.apple.com/videos/play/wwdc2021/10170/
- ⭐ https://wwdcbysundell.com/2021/working-with-in-app-purchases-in-storekit2/
- ⭐ paid for video 20$ ishh: https://www.udemy.com/course/storekitone/
- ⭐⭐⭐ Simple and has all info: (paid article) https://www.kodeco.com/books/real-world-ios-by-tutorials/v1.0/chapters/14-deploying-to-the-app-store
- ⭐⭐⭐⭐⭐ Rich content on distribution on appstore (payed) https://www.kodeco.com/books/ios-app-distribution-best-practices (use 12ft to unlock)