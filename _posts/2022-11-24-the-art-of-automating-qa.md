My notes on automating quality assurance <!--more-->

<img width="480" height="261" alt="Gif of ghost in a shell typing on keyboard" src="https://media.giphy.com/media/9CffOPMLx0Hf2/giphy.gif">

> Build something elegant with high utility that is easy to maintain

**Pros:**
- Automated testing provides faster development velocity by catching bugs instantly.
- Tests can be run on demand on multiple devices, making it easier to scale and catch issues that humans might miss.
- Automated testing covers more ground than humans, including testing OS updates, device sizes, and device configurations.
- Automated testing is more ethical than using humans to do repetitive and mundane tasks for low pay.
- Automated testing promotes a strong validation culture by validating feature benefits versus the maintenance complexity they potentially add.
- Engineers love getting immediate feedback from code, and automated QA tests either fail or succeed and return detailed information on why things failed.
- Automated testing forces you to be realistic about what you can maintain with the resources at your disposal.
- Automated testing forces businesses to define a testing plan.
- Creating robots that work for you around the clock for free is fun.
- Automated tests are always available, unlike remote testing teams which can be expensive and take time to return failure information that is often hard to reproduce.
- Automated tests can be run in random order with random mock data and setup configurations, covering more ground in less time than any amount of humans could ever cover.

**Cons:**
- Setting up automation can be time-consuming and require significant effort.
- Deploying automation typically requires advanced coding skills.
- Automation often requires access to your codebase, unless you use a tool like Waldo.
- The initial cost of implementing automation can be high, but it can become more cost-effective over time.

### Options
- Apples mobile QA testing suite called XCUITest
- Web technologies such as selenium and playright
- Sass services such as: [https://waldo.com/](https://waldo.com/)
- XCUITest saas: [https://saucelabs.com/](https://saucelabs.com/)

<img width="480" height="271" alt="Mad man actor vacation mode" src="https://media.giphy.com/media/3oEdv9duTLhWoNhcGs/giphy.gif">

### Concept
- An automated "QA playlist" can help streamline your testing process.
- Running your tests in the cloud can provide greater scalability and accessibility.
- Testing on multiple devices, operating systems, and accessibility configurations can help ensure your app works for all users.
- Instant feedback for all changes and updates can help catch bugs early in the development process.
- Receiving a screenshot via email when a test fails can help you quickly identify and fix issues.

<img width="480" height="270" alt="Whack a mole" src="https://media.giphy.com/media/aWNByu8u6sati/giphy.gif">

### Rational
- Automated QA can help developers create better, faster, and stronger software.
- Automated QA can provide developers with ammunition when arguing against increasing complexity.
- Automated QA can force businesses to be mindful about complexity versus available resources.
- Automated QA can provide peace of mind for developers and businesses.
- Automated QA can help ensure that software works with changing APIs.
- Automated QA can be refined and improved when developers are less utilized.
- Adding complex mock data can help avoid future customer complaints.
- Testing extreme cases can help increase performance.
- Testing for edge cases can help increase customer satisfaction.
- Automated tests can help ensure that software runs safely on thousands of different system configurations.
- Developers have peak knowledge just when the software enters MVB or MVP, making this peak knowledge essential when writing automated QA efficiently with high utility.
- Regression, customer data, app store reviews, support, and API changes can suddenly take weeks instead of hours once users are onboarded.
- People don't want to be guinea pigs when testing beta software, so it's important to make sure things work before showing them the software.
- Automated QA can help reduce security threats and GDPR vulnerabilities.
- Having an automation mindset is a valuable transferable skill in today's job market.
- User story tests can help ensure that things keep working when iOS is updated for the user.
- Security and stability are highly sought after by today's customers, as a crash can mean users lose data and their lives get complicated.
- Time spent setting up and maintaining automated tests far outweighs the hassle of manually testing each minuscule thing and getting stuck in a rejection loop with the Apple App Store review board.
- Constantly running tests can warn you if an external API goes down, giving you time to fix it before most of your users notice it.
- App Store reviewers act as gatekeepers for the Apple App Store and may not care about your app, so having systems in place to weed out edge cases can help you pass their reviews.
- Iterating becomes a lot easier if you can verify that each iteration doesn't break the system.

### How to write a test-plan:
- Define user stories. Here are some examples for a music app:

```
Playlist-scenes:
- Create playlist
- Rename playlist
- Remove playlist
- Add song to playlist
- Remove song in playlist

Prefs-scenes:
- Change password
- Change email
- Sign out user

Album-scenes:
- Play song
- Favorite album
- Favorite song in album
- Shuffle song

Auth-scenes:
- Login w/ username and password
- Sign up with mock data
- Resend password to email
```

- Here is a QA Test prototype for debugging a playlist feature:

<img width="320" alt="img" src="https://github.com/stylekit/img/blob/master/test_af.gif?raw=true">


### Example Automated QA code:

```
func loginTest() {
	.tapTextField(label: "userName"),
	.type("John"),
	.tapTextField(label: "password"),
	.tapButton(label: "OK")
}
login() // login user
```

### Resources
- My personal go to testing library: [https://github.com/eonist/TestRunner](https://github.com/eonist/TestRunner)
- UITesting blog post
- [Raywenderlich.com](Raywenderlich.com)
- Maker book chapter on Automating
- Github book about collaboration the CI chapter
- Your unit testing posts
- [https://github.com/mozilla-lockwise/lockwise-ios/blob/master/docs/test-plan.md](https://github.com/mozilla-lockwise/lockwise-ios/blob/master/docs/test-plan.md)
- [https://github.com/mozilla-lockwise/lockwise-ios/blob/master/docs/test-plan-autofill.md](https://github.com/mozilla-lockwise/lockwise-ios/blob/master/docs/test-plan-autofill.md)
- Monitoring service for API’s [https://updown.io](https://updown.io)
- Definition of CT: Continuous testing is the process of executing automated tests as part of the software delivery pipeline to obtain immediate feedback on the business risks associated with a software release candidate. https://en.wikipedia.org/wiki/Continuous_testing


### To summarise:
- 🦾 Automating QA can be more effective than manual QA, catching bugs faster and more consistently.
- 🆓 QA automation doesn't have to be expensive, as there are many affordable tools and services available.
- 🏝 QA automation can help improve work/life balance by reducing the need for manual testing and allowing developers to focus on other tasks.
- ♻️ Design, code, test, repeat: the essential cycle for creating high-quality software.
- ☁️ Running automated QA in the cloud provides greater scalability and accessibility.
- 🔌 Connecting QA automation to communication tools like Slack, email, and GitHub streamlines the testing process.
- 🌅 Building QA automations from the start ensures that software is thoroughly tested from the beginning.
- 📈 QA automation is highly scalable and can be adapted to meet the needs of any project.
- 😸 Setting up automation infrastructure can be a fun and rewarding experience for developers.

<img width="480" height="460" alt="img" src="https://media.giphy.com/media/nmM68Wdn0Ngc0/giphy.gif?raw=true">

### Todo:
- Discuss the benefits of integrating cloud services with GitHub Actions for streamlined development.
- Include relevant links to provide readers with additional resources and context.
- Edit and refine the text for improved readability and engagement.
- Add an article index to help readers navigate the content more easily.
- Explore the future of QA automation with machine learning and AI, and how it can improve testing accuracy and efficiency.
- Explain why adding complexity outside of your product is often a better strategy than adding complexity inside your product.
- Emphasize the importance of automating repetitive tasks for sustainable growth of your product offering.
- Discuss the potential benefits and challenges of training QA robot models for automated testing.
