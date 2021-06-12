My notes on splitting up projects into extensions <!--more-->

### Arguments
- Blog post on the subject: https://medium.com/@KaushElsewhere/better-way-to-manage-swift-extensions-in-ios-project-78dc34221bc8
- Ultra mega controllers are not very readable. I doubt many would argue that 1000++ line files  are easy to reason about.
- In the end its an opinionated methodology, but so is writing 1000++ LOC files and declaring them comprehensible
- Already common way to organise delegate extensions (See list of popular opensource projects using this methodology at the bottom of this page)
- Small files are easier to stomach. If every file serves only one topic, you know quickly where to look. You also immediately know what does not belong to the topic, without having to read through commentary.
- Multiple files allow for non-linear organisation. The building blocks of a program rarely follow a single, linear chain of interactions. Loosely coupled components are easily represented by individual files, and folders allow to add external structure.
- Distinct files are easier to reorganise. As complexity grows, components move to sub packages, and sometimes you just need to clean up. A file can simply be moved as a whole. Copy/Pasting to migrate code is more work, especially if you have tacked on all the structuring manually.
- Individual files are easier to track. Code is ideally in a version control system. Knowing that backend/Service+Json.swift changed is already quite informative, and lets you know whether the change is relevant for you. If everything is a single file, you get line 254–378 in Service.swift changed, and have to look up the context yourself.
- Basically, a single file is easy to write. Multiple files are much easy to read, maintain and manage. For software development, the later is much more important. Even if you are working alone, future-you does not know everything that past-you has done.
- Makes merging easier. Less likely to create merge conflicts (Important when we grow as a team)
- It also lets you increase method code quality. If a method is 20 lines++ (common in this project)  its already too big and could use some refactoring. But adding another support method to extract functionality out, only adds to the length of the class. However. If you have grouped the MVC class into separate extension files. You can effectively create private methods that doesn't touch other parts of the class. as they are restricted to the actual file. not the class. As such you can extract individual parts of the Mega-Methods into smaller, even reusable methods for that file. A lot of time related methods use similar code chunks. Which if you don't reuse them. It just worsens the overall health of the code-base.
- It makes you aware of related code, which you can further abstract and put into lower-level modules. Often times local extension methods of other classes.
- And at any rate, people often complain about fat view controllers and breaking a view controller up this way helps keep it better organised, even if it doesn't actually make the view controller thinner.
- As the implementer of a feature, You are allowed some implementer autonomy to chose solutions and which support methods I will use.
- **Seven** is considered the number of items that our brain can observe at the same time without getting confused. Above 7 we find it difficult to keep an overview of what we see. Therefore: each class should not have more than 7-10 methods. A class that has more than 10 method is probably too complex and you should try to split it. Splitting is a very effective method because every time you split a class you reduce the complexity of each individual class at least by a factor of 2.
- App generally has challenges in terms of code quality. (If you check with codebeat.co your project will probably get a B+ or C+) It usually relates to overcrowded classes that do too many things and lack of compartmentalisation. We should strive to move code out to the **edges of the source** and into smaller chunks. But that isn't always realistic in the short term, because there is a lot of cross related "spagethi" 😅 code. Moving things into Extensions in separate files is a non-destructive initiative towards this goal of real modularisation. An initiative that doesn't create havoc on some random place in the code base. Another non-destructive initiative is to add commentary to the code. Some devs claim you should be able to derive context from the source code. And that is true. But its unrealistic as the project grows. Your mind can only hold a small snap-shot of how things work at the time, especially as we add more developers. Things will shift around from day to day, and you simply cant keep all context in your head. Abstract and artefacts would help greatly in getting the context of why things exist. So you can quickly get in and out of a particular area of the code and solve the feature efficiently.
- Influential projects using similar file-extension methodology:
https://github.com/wordpress-mobile/WordPress-iOS/tree/develop/WordPress/Classes/Models 3k ⭐️
https://github.com/mozilla-mobile/firefox-ios/tree/master/Client/Frontend/Browser/BrowserViewController 8k ⭐️
https://github.com/louisdh/panelkit/tree/master/PanelKit/PanelManager 3.8k ⭐️
https://github.com/vapor/vapor/tree/master/Sources/Vapor/Routing 17.3k ⭐️
https://github.com/HeroTransitions/Hero/tree/master/Sources/Transition 17k ⭐️
https://github.com/DeclarativeHub/Bond/tree/master/Sources/Bond/Observable%20Collections 3k ⭐️
https://github.com/AudioKit/AudioKit/tree/master/Examples/macOS/AudioUnitManager/AudioUnitManager 7k⭐️
https://github.com/SvenTiigi/WhatsNewKit/tree/master/Sources/Configuration 1.5k⭐️
https://github.com/steve228uk/MessengerKit/tree/master/MessengerKit/Messenger%20View 1.9k ⭐️
https://github.com/lexrus/VPNOn/tree/develop/VPNOnKit 4k⭐️
https://github.com/insidegui/WWDC/tree/master/WWDC 7k ⭐️
https://github.com/gaowanli/MyCoin/tree/master/MyCoin/Classes/Utilities/AppDelegates 150⭐️
etc etc
