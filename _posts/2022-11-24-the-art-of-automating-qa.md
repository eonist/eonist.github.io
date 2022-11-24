My notes on automating quality assurance <!--more-->

⚠️️ This post is currently WIP (pre-released for early feedback)⚠️️

<img width="480" height="261" alt="Gif of ghost in a shell typing on keyboard" src="https://media.giphy.com/media/9CffOPMLx0Hf2/giphy.gif">

> Build something elegant with high utility that is easy to maintain

**Pros:**
- More development velocity because bugs can be caught instantly
- You can run these tests on demand on multiple devices
- It scales when you expand your offering. catches thing humans miss
- Covers more ground than humans. Test OS updates. Device sizes. Device configs.
- More ethical than using humans to do repetitive and mundane tasks for low pay
- Promotes a strong validation culture. Validating feature benefit vs the maintenance complexity it potentially adds
- Engineers love getting immediate feedback from code. Automated QA test either fail or succeed, and return detailed info why things failed
- It forces you to be realistic about what you can maintain with the resources at your disposal
- Forces business to define a testing plan
- It's fun to make robots that work for you around the clock for free
- Automated tests are always available (Remote testing-teams cost $$$ and take time to return failure info, and the info is many times hard to reproduce, flakey)
- You can run automated tests in random order with random mock data with random setup configs. On a scale and in way less time that no amount of humans could ever cover.

**Cons:**
- Initial setup is takes time and effort
- Requires high-level coding skills to deploy
- Requires access to your codebase (unless you use something like waldo)
- Can be expensive at first, and then gradually cheaper

### Options
- Apples mobile QA testing suite called XCUITest
- Web technologies such as selecium
- Sass services such as: https://waldo.com/

<img width="480" height="271" alt="Mad man actor vaction mode" src="https://media.giphy.com/media/3oEdv9duTLhWoNhcGs/giphy.gif">

### Concept
- An automated "QA playlist"
- Run it in the cloud (commit, pr, time interval)
- Multiple devices, OS, Device-types, Accessibility-configs, Regression
- Instant feedback for all changes and updates
- Get a printscreen emailed to your mailbox when a test fail

<img width="480" height="270" alt="Whack a mole" src="https://media.giphy.com/media/aWNByu8u6sati/giphy.gif">

### Rational
- Better, faster, stronger
- Gives developers ammunition when arguing against increasing complexity
- Forces business to be mindful about complexity vs available resources
- Peace of mind for devs and business, look ma, no hands
- Better product -> Happy customers -> more revenue
- Software doesn't exist in a vacuum. APIs change rapidly.
- And we rely on moving forward adding features and improving. To be able to do that efficiently we need an infrastructure that lets us validate that out latest updates works as good as the last one.
- Automated QA can be refined and improved when devs are less utilised
- Add complex mock data to avoid future customer complaints. Customers do things you never thought of. And will often vote 1 star on the app-store if their need isn't met.
- Increase performance by testing extreme cases
- Increase customer satisfaction by testing for edge cases that hit the 1%. Human QA only every cover 80-90% use cases.
- Why would your software run safly on 1000s of different system configs if they cant run automated Tests
- Your developer has peak knowledge just when the software enters MVB or MVP. This peak knowledge is essential when writing automated qa efficiently with high utility
- When you dont have any users and customers everything is jolly. You can cahnge anything. Once you onboard users you have to deal with regression, customer data, appstore reviews, support, api-changes suddenly take weeks not hours.
- People dont really want to be your guinni-pig. When people test your beta their interested to see what all the fuss is about. their not interested in reporting if a button doesnt work. So make sure things work, then show them the fuss.
- Less security treats, less gdpr vunerabilities
- Having an automation mindset is a valuable transferable skill in this day and age
- user-story-tests ensure things keep working when iOS is updated for the user. We can test iOS beta version ahead of time before they are installed on users devices etc
- security and stability is highly sought after by todays customers A crash can mean users loose data which can mean their lives get complicated, and by extension we are responsible.
- Simply put: Time spent setting up and maintaining Automated-tests far outweight the hassle of manually testing each miniscule thing and getting stuck in a rejection loop with apple app store review board.
- Constantly running tests can also warn you if an external API goes down, giving you time to fix it before most of your users notice it.
- App-store reviewers that act as gatekeepers for the apple appstore do not care about your app. These gate-keepers have one mission and that is to make your app fail. And they know how to find that edge case nobody will ever stumble upon in real life. As such the best strategy is is to have systems in place to weed out these edge cases. Because you will need to pass these gatekeeprs for every update you ever make. And each gatekeeper will have different strategies to trip you up.
- Iterating becomes a lot easier if you can verify that each iteration doesnt break the system.

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
- Raywenderlich
- Maker book chapter on Automating
- Github book about collaboration the CI chapter
- Your unit testing posts

### To summarize:
- 🦾 Automating QA > Manual QA
- 🆓 QA automation != expensive
- 🏝 QA automation is great for your work / life balance
- ♻️ Design, Code, Test. Repeat
- ☁️ Run your Automated QA in the cloud all day every day.
- 🔌 Connect your QA automation to slack, email and github
- 🌅 Build QA automations from the start
- 📈 QA Automation is extremely scalable
- 😸 Automation is fun

<img width="480" height="460" alt="img" src="https://media.giphy.com/media/nmM68Wdn0Ngc0/giphy.gif?raw=true">

### Todo:
- write about Cloud integration with github actions
- add links
- clean up text
- add article index for increased readability
- write about the future of QA automation with machine learning and AI
