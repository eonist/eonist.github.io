My notes on adding apps to Apple app store<!--more-->

### Here’s how to submit your app to the Apple App Store:

- Create an iOS distribution provisioning profile and distribution certificate
- Create an App Store Connect record for your app (Add new app)
- Archive and upload your app using Xcode (Select Generic iOS Device) then `product - archive` click distribute app to upload
- Configure your app’s metadata and further details in its App Store Connect record
- Submit your app for review
- Check on the status of your app

### Testflight
- the first build of your app must be approved by TestFlight App Review (This build is automatically sent for review when you add it to a group.)
- Upload a beta build to appstore connect (Builds remain active for 90 days after upload)
- Internal testers (100 org memebers)
- external testers (10k people) email addresses or by enabling and sharing a public link ( go to your app’s TestFlight page, click an existing group, and click Enable Public Link.)
- When sharing your app with external testers, you’ll need to let them know what to test and any other important information relevant to testing. Provide these details on your app’s Test Information page in App Store Connect.

### Find message that resonates with target user:
- Identify core user problem / pain
- Present innovative solution that solves the problem

### Organic search
Keywords, metadata, app name, description,

#### Title (App name)
Your app’s name plays a critical role in how users discover it on the App Store. Choose a simple, memorable name that is easy to spell and hints at what your app does. Be distinctive. Avoid names that use generic terms or are too similar to existing app names. An app name can be up to 30 characters long.

#### Subtitle
Your app’s subtitle is intended to summarize your app in a concise phrase. Consider using this, rather than your app’s name, to explain the value of your app in greater detail. Avoid generic descriptions such as “world’s best app.” Instead, highlight features or typical uses of your app that resonate with your audience. You can update your subtitle when submitting a new version of your app to help you determine the subtitle that’s most effective for engaging users. A subtitle can be up to 30 characters long and appears below your app’s name throughout the App Store.

#### Description
- Provide an engaging description that highlights the features and functionality of your app. The ideal description is a concise, informative paragraph followed by a short list of main features. Let potential users know what makes your app unique and why they’ll love it. Communicate in the tone of your brand, and use terminology your target audience will appreciate and understand. The first sentence of your description is the most important — this is what users can read without having to tap to read more. Every word counts, so focus on your app’s unique features.
- If you choose to mention an accolade, we recommend putting it at the end of your description or as part of your promotional text. Don’t add unnecessary keywords to your description in an attempt to improve search results. Also avoid including specific prices in your app description. Pricing is already shown on the product page, and references within the description may not be accurate in all regions.
- You can update your app’s description when you submit a new version of your app. If you want to share important updates more frequently, consider using your promotional text instead.
- Focus the bulk of your energy on the first three lines of your description to immediately grab your reader’s attention. Given the ever-growing number of apps in the marketplace, customers are sure to have a few — if not several — alternatives to consider when evaluating yours. Make their decision easy by immediately communicating what it does and why they should use it.

### Promotional text
Your app’s promotional text appears at the top of the description and is up to 170 characters long. You can update promotional text at any time without having to submit a new version of your app. Consider using this to share the latest news about your app, such as limited-time sales or upcoming features.

#### Screenshots
- Use images captured from your app’s UI to visually communicate your app’s user experience. You can feature up to 10 screenshots on your App Store and Mac App Store product pages. Depending on the orientation of your screenshots, the first one to three images will appear in search results when no app preview is available, so make sure these highlight the essence of your app. Focus each subsequent screenshot on a main benefit or feature so that you fully convey your app’s value. If your app supports Dark Mode, consider including at least one screenshot that showcases what the experience looks like for users.
- [App Store Connect Help](https://help.apple.com/app-store-connect/#/devd274dd925)
- You have to upload 2 sizes of screenshots. launch simulator for iphone6 and iphoneX and click screenshot button to grab some shots
- for iPhone 5.5" Display the trick is to take a shot and then resize in preview to 1242 x 2208 (the extra pixel must be removed to the height)
- for mac:  1280x800 1440x900 2560x1600 2880x1800

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
- Update frequently
Mobile customers are looking for apps that are constantly improving, with regular updates based on customer feedback. Apps that are frequently updated are seen, by both the app store and the customer, to be of a higher value and more customer-centric.

#### Ratings and reviews
- Ratings and reviews influence how your app ranks in search and can encourage users to engage with your app from search results, so focus on providing a great app experience that motivates users to leave positive reviews. Individual ratings inform your app’s summary rating, which is displayed on your product page and in search results. This summary rating is specific to each territory on the App Store.
- The SKStoreReviewController API lets you give users an easy way to provide feedback about your app. You can prompt for ratings up to three times in a 365-day period. Users will submit a rating through the standardized prompt, and can write and submit a review without leaving the app.
- You can use App Store Connect to respond to customer reviews of your app to directly address their feedback, questions, and concerns. When you respond, the reviewer will be notified and will have the option to update their review. Reviews and responses can be updated at any time, but only the latest review and response for each user will display on your product page.

#### Categories
Categories on the App Store help users discover new apps to meet their needs. You can assign a primary and a secondary category to your app. The primary category is particularly important for discoverability, as it helps users find your app when browsing or filtering search results, and it determines in which tab your app appears on the App Store. Be sure to select the primary category that’s most relevant. Choosing categories that are not relevant to your app may cause your app to be rejected when submitted for review.

#### Gotchas:
- Bundle ID – here you can select from the app identifiers that are registered in the Developer Account (this value is unique for every app on the App Store). If the app build is ready, make sure you choose the same ID that was used in the Xcode during app archiving.
- you can specify the app price for a specific time period. It is useful if you want to make your app cheaper at the beginning to  encourage users to download it
- sku nr: for profit record keeping. you can use app id, or date or version. `NameOfAppV1` y2022m12d04
- ⚠️️ When you create a new build, you have to increment the build nr in general - build nr ⚠️ ️
- Click save button a lot when filling out apples app store info pages (its a lot to fill out can take a day to finish)
- Apple app review is a bit random, you might get rejected for wrong reasons etc. So make sure you have all the obviouse things in check. Like the design and UX works as expected with out bugs etc. or else the review will be a lot of back and forth

### FAQ:
- Do I need to get approval to start testing beta builds?
- To make your beta builds available to members of your App Store Connect team, your app doesn’t need review by TestFlight App Review. To make your beta builds available to external users, your beta build may require review. When you add the first build of your app to a group, the build gets sent to TestFlight App Review to make sure it follows the App Store Review Guidelines. A review is only required for the first build of a version and subsequent builds may not need a full review. Testing can begin once a build is approved.

### Tips:
- The fastest and easiest way is just to launch your app in the iOS simulator (for the screen size that you need), navigate to the screen you want to take a screenshot of and then hit CMD+S on your keyboard.
- use freeprivacypolicy.com to generate priv policy. Store in a html on a website or github gist (more info: https://developer.apple.com/app-store/review/guidelines/#privacy)

### Resources:
- Review guidelines [https://developer.apple.com/app-store/review/guidelines](https://developer.apple.com/app-store/review/guidelines)
- Bezels and badges: [https://developer.apple.com/app-store/marketing/guidelines/](https://developer.apple.com/app-store/marketing/guidelines/)
- App store submission checklist: [https://clearbridgemobile.com/how-to-submit-an-app-to-the-app-store/](https://clearbridgemobile.com/how-to-submit-an-app-to-the-app-store/)
- another checklist: https://www.thedroidsonroids.com/blog/how-to-submit-app-to-the-app-store-guide-and-checklist
- in app purchase: https://developer.apple.com/in-app-purchase/
- 100 promo codes: https://help.apple.com/app-store-connect/#/dev50869de4a
- promoted in-app purchases: https://developer.apple.com/app-store/promoting-in-app-purchases/
- Overview off appstore functionality https://developer.apple.com/app-store-connect/api/
- Testflight tutorial: https://reinteractive.com/posts/344-how-to-upload-your-ios-app-to-testflight-for-distribution-beta
- Add app to appstore (a bit outdated) https://reinteractive.com/posts/344-how-to-upload-your-ios-app-to-testflight-for-distribution-beta
- Bundle apps from different platforms in one bundle purchase https://developer.apple.com/support/universal-purchase/
- For mockups: https://mockuphone.com/
- Big app store submission tut. https://medium.com/appmanager/uploading-an-ios-app-to-the-app-store-e6a218ecb556
