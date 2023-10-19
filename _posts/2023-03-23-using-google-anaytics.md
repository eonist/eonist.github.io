My notes on Google Analytics <!--more-->

### Setup:
1. Set up Google Tag Manager.
2. Create a Google Analytics account.
3. Set up an analytics tag with Google Tag Manager.
4. Set up goals.
5. Link to Google Search Console.

### Create Events to Track Interactions
Events track user interactions with content on a web page or screen load. Event tracking has four components:
- Category
- Action
- Label (optional, but recommended)
- Value (optional)

For instance, if you want to track how many times a user downloaded a file, you could use:

```
Category: “File”
Action: “Downloaded”
Label: “e-book”
```

### KPI:
- The KPIs you choose to track should always connect back to the ultimate goals of your website. For example, if you are a company that uses social media to sell bespoke kitchen knives on your website, then you would want to track your social media traffic volume and how many conversions you’re getting from social media marketing campaigns month over month.
- The objective is to find metrics, like purchases, downloads, or signups, that tell you whether you are moving closer to your organization’s high-level goals. Examine your marketing funnel, determine which KPIs will help you understand how well your current marketing efforts are performing, and then start learning how to track them with Google Analytics.

### Behavior KPIs:
Metrics that tell you how users engage with your website and move along their buyer’s journey.
- Time-on-page: An important metric that gives you an idea of how much people are engaging with your content.
- Bounce rate: A “bounce” is when someone clicks on your page and does not move on to any other pages. A landing page should have a lower bounce rate than a receipt page, for example.
- Exit page statistics: These pages are the last pages users see before leaving your website. High exit rates on a particular page might show that it is poorly optimized.

### Outcome KPIs:
Metrics that track goals and conversions on your properties.
- Goal conversions: How many times you get a conversion, a completed action that contributes to your primary digital marketing goals, over a period of time.
- Return on ad spend (ROAS): How much money you make in sales/conversions from ads after accounting for the money you spent on the ads.
- Cost-per-click (CPC): The cost of the average click generated from a PPC campaign.

### Acquisition:
Metrics concerned with how readers get to your site.
- Organic traffic volume: The amount of traffic that comes to your site from unpaid search.
- Pay-per-click (PPC) traffic volume: The amount of traffic that comes from paid links on search.
- Referral traffic volume: The amount of traffic that comes from links on other websites.
- Direct traffic: Traffic that comes from other sources, including when someone types in your URL.

### Goals:
Destination Goals: A goal that is met by a user reaching a certain page or destination on your website.
Example: A user reaches the checkout page of your website.

Duration Goals: A goal that is met by a user spending a certain amount of time on a page or your site.
Example: A user stays on one of your recipe pages for more than 10 minutes.

Event Goals: A goal that is met by a user performing an action on your website.
Example: A user downloads your eBook.

Pages/Screens Per Session Goals: A goal that is met when a user visits a preset number of pages on your site.
Example: A user explores five different pages on your website in a session.

### Uscases for analytics for mobile apps
- Understand the number of users in your app, their characteristics, and where they come from.
- Measure what actions your users are taking.
- Visualize user navigation paths.
- Slice and dice your data to understand different user groups' behavior

### Information
- Events: What is happening in your app, such as user actions, system events, or errors.
- User properties: Attributes you define to describe segments of your user base, such as language preference or geographic location.

### Resources:
- Adding exceptions to Google Analytics: [https://stackoverflow.com/questions/21718481/report-for-exceptions-from-google-analytics-analytics-js-exception-tracking](https://stackoverflow.com/questions/21718481/report-for-exceptions-from-google-analytics-analytics-js-exception-tracking)
- ✨: [https://www.owox.com/blog/articles/guide-to-mobile-app-analytics/](https://www.owox.com/blog/articles/guide-to-mobile-app-analytics/)
- GA4 for mobile: [https://uxcam.com/blog/google-analytics-mobile-apps/](https://uxcam.com/blog/google-analytics-mobile-apps/)
- ✨ Analytics concept: [https://www.netguru.com/blog/analytics-in-mobile-app](https://www.netguru.com/blog/analytics-in-mobile-app)
- [https://github.com/sentryco/Telemetry/](https://github.com/sentryco/Telemetry/)

### Gotchas:
- Firebase analytics sdk uses GA4 under the hood
- GA3 will be turned off july 2023
- Limits of Google Analytics 4: [https://support.google.com/analytics/answer/9267744?hl=en&ref_topic=9756175](https://support.google.com/analytics/answer/9267744?hl=en&ref_topic=9756175)
