My notes on release notes<!--more-->

> application release notes are technical documents that accompany software products in beta stages or software products that have been enhanced or repaired.

### Purpouse:
-  It acts as a knowledge base of what has changed since the previous release

### Do's
- Use plain language
- Keep them short
- Group them logically
- Include relevant links
- Don't be too technical
- Add a bigger micro copy for major releases

### Minimum release notes:
 - bug fixed, performance improvement
 - Bug fixes and enhancements

### Example:
```
Release Version 1.2 (June 27, 2011)
New Features: (Or Whats new?)
- Added support for Ecommerce hits. 🛍
- Added Debug flag. 🏴‍☠️
- Added DryRun flag. 🇸🇪
- More descriptive error information on SQLite3 failures 💿
Bug Fixes:
- Fixed crash when using custom variables. 🐛
- Fixed memory leaks when using custom variables. 💦
```

### Another example:
```
Added
- macOS: Added support for USB YubiKeys (native MacCatalys builds only; won't work in iPad simulation mode on Apple Silicon devices) (#217)
- Ability to copy references to entry fields (tap the field to see the copy button) [thanks, u/RandomComputerFellow]

Improved
- Added a button to delete custom entry fields [thanks, YJ]
- Display full Pro app name on the Home screen
- Minor UI improvements focused on macOS
- Updated all translations

Fixed
- macOS: too frequent Touch ID popups. Now one needs to click a "Touch ID unlock" button first (#237) [thanks, everyone]

```
### Resources:
- https://amoeboids.com/blog/release-notes-templates-guide/
- https://slite.com/learn/release-notes
- https://www.indeed.com/career-advice/career-development/release-notes-template
- Advance release notes: https://amoeboids.com/blog/50-release-notes-examples-to-inspire-you/
- Gd: https://www.releasepad.io/the-complete-guide-on-how-to-write-perfect-release-notes-in-2022/
- Release notes as a service: https://www.releasepad.io/
- Nice release note structure: https://github.com/mozilla-lockwise/lockwise-ios/blob/master/docs/release-notes.md

### Where to post release notes:
- Email
- Blog posts
- Website
- Social media
- App Store/Google Play Store

### How Do You Write Release Notes for Iphone Apps?
When writing app release notes for an iPhone app, there are certain important factors to consider. First, you need to make sure the notes are concise and clear. Release notes should be easy to read, so use simple language and break up text with headings or bullets points if possible. It’s also helpful to list changes in numeric order, starting with the largest change first and then breaking down into smaller changes.

You also want to include information on how users can update their apps. Make sure it’s easy for users to understand how they can update the app by walking them through each step of the process. You should also provide links or screenshots of where users can find this information in their settings or App Store page. Finally, it’s important that you provide a link for people who need help with updating the app or have other questions about the new release.

### How Do You Write Release Notes for Android Apps?
When writing release notes for Android apps, it’s important to provide concise and clear information. Release notes should include details about the changes made in each version, such as bug fixes, new features or improvements. Additionally, any important steps users need to take after updating should also be included. Depending on the purpose of the release notes, other information such as security patches may also be included.

When writing release notes for Android apps, it is recommended to use a consistent format throughout different versions. This makes the notes easier to read and helps users quickly find the relevant information they’re looking for. In addition to including details about changes made in each version, screenshots and videos can also help demonstrate what has changed in a more visual way and make it easier for users to understand how features work. Finally, it’s important to update existing release notes rather than creating new ones for every new version so that all relevant info is stored in one place and easy to locate by customers.

### Tempalte:
```
Here's a template you can reference to help you write your release notes:

[Document name]

[Product name]

Release date: [product launch date]

Release notes version: [version number]

Release notes date: [document creation date]

Overview

[Briefly describe the product's new features, changes or updates. Explain bug fixes, key functionality alterations or useful elements. Keep the user in mind when deciding how to summarize your release.]

Features

[List a new or exciting feature and how to use it.]
[List a new or exciting feature and how to use it.]
[List a new or exciting feature and how to use it.]
Changes

[Describe an update and how the user can navigate them.]
[Describe an update and how the user can navigate them.]
[Describe an update and how the user can navigate them.]
User Impacts

[Include any changes that might affect the user. List the impact and the reason for the modification.]
[Include any changes that might affect the user. List the impact and the reason for the modification.]
[Include any changes that might affect the user. List the impact and the reason for the modification.]
Notes

[Add any disclaimers or additional information about your product that a user may find useful. You can also include links to product tutorials or locations for users to find more information.]

Support

[Explain how users can contact you if they have questions, comments or concerns about your product. List important information like availability and preferred communication channels.]
```

### example:
```
Release notes example
Here's an example of completed release notes you can use for reference:

Flypitch Mobile Banking App Release Update

Flypitch Mobile Banking App

Release date: Nov. 21, 2021

Release notes version: 2

Release note date: Sept. 25, 2021

Overview

Take control of your money with the secure mobile banking app. This new app version offers increased functionality, improved performance and bug fixes. Security updates ensure your information always stays safe.

Features

Remote check deposit: Users can now deposit checks anytime, anywhere using their camera and our in-app deposit feature.
Easy log-in: App improvements allow users to stay logged in on their device for faster and more convenient access to their account.
Quicklook: Locate your balance and recent transaction details in the app with a single swipe by using the Quicklook feature.
Changes

Bug fixes: Bug fixes improve security functions and remote storage options, keeping your information off your device in case your phone or tablet becomes lost or stolen.
Personalized user info: New account page offers customizable customer information, like contact information and a photo upload tool.
Send money: Updates to our immediate transfer feature allow you to send money to other Flypitch account holders instantly.
User impacts

Software requirements: Update requires IuS version 8.2.3 for optimal functionality.
Permission changes: Requires permission to collect sensitive data for instant transfers and camera access.
Notes

Anyone can download the app, but you must be an existing FlyPitch customer to log in and use the app features.

SupportConnect with FlyPitch by calling our 24/7 customer service line at 1-800-555-5555. For questions, concerns or comments, please email our support team at FlyPitchCustomerSupport@email.com.
```

From mozilla lockwise release notes:
the link to commit is kinda nice
```
1.6.2 (Build 3616)

Date: 2019-06-26

We fixed bugs related to: signing in, syncing, and translations.

Changes since the last build:

import the latest fr and de translations (#1067)
bump application-services to v0.32.1 (#1068)
All changes since version 1.6.1:

migrate to Swift 5 (#1037)
update application-services to 0.31.2 (#1050)
implement a sync timeout (#1045)
hide password after navigating away (#1049)
increase cancel button width in AutoFill list view (#1048)
improve error handling and reporting for better troubleshooting (#1038)
added minimal support for when no network is available (#1054)
display the translated string for text view in onboarding (#1057)
import the latest fr and de translations (#1067)
bump application-services to v0.32.1 (#1068)
```
