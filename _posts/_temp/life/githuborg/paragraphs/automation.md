
### Continuous quality 👌
Bellow is a list of noticeable swift projects on github, sorted by code quality. As you can see popularity and big tech names doesn't always translate to great code quality. On the contrary, passion projects by indie developers such as [SindreSorhus](https://github.com/SindreSorhus) and  [3lvis](https://github.com/3lvis/)  can often yield the highest code quality.

Author | Project | LOC | Grade | [GPA](https://hub.codebeat.co/docs/gpa-explained)
--- | --- | --- | --- | ---
*Still* | `renders` | **nicely** | `renders` | **nicely**
1 | 2 | 3 | 2 | 3

### Test

Author | Project | LOC | Grade | [GPA](https://hub.codebeat.co/docs/gpa-explained)  | Stars | [CodeCov](https://codecov.io/)
--- | --- | --- | --- | ---
[SindreSorhus](https://github.com/SindreSorhus)  | [gifski](https://github.com/SindreSorhus/gifski-app) | 788 | `A` | 3.63 | **2331** | NA
[3lvis](https://github.com/3lvis/)  | [Networking](https://github.com/3lvis/Networking) | 1110  | `A` | 3.55 |  **1100** | NA
[Eonist](https://github.com/eonist)  | [HybridCamera](https://github.com/eonist/HybridCamera)  | 756 | `A` | 3.41 | **5** | NA
[Eonist](https://github.com/eonist) | [Element](https://github.com/eonist/Element)  | 5718 | `A` | 3.38 | **815** | NA
[Linkdin](https://github.com/linkedin/LayoutKit) | [LayoutKit](https://github.com/linkedin/LayoutKit) | 4650 | `B` |   3.22 | **2557**  | 93%
[IBM](https://github.com/IBM-Swift/)  | [Kitura](https://github.com/IBM-Swift/Kitura) | 3880 | `B` | 2.96 | **6577** | NA
[Finn](https://github.com/finn-no) | [Paw](https://github.com/finn-no/Paw) | 1332 | `B` | 2.87 | **9** | NA
[AlamoFire](https://github.com/Alamofire/) | [AlamoFire](https://github.com/Alamofire/Alamofire)  | 6733  | `B`   | 2.77  | **28426**  | NA
[Apple](https://github.com/apple/) | [apple-ios-samples](https://github.com/robovm/apple-ios-samples) | 119655  | `B`   | 2.76  | **NA**  | NA
[Ramotion](https://github.com/Ramotion)  | [animated-tab-bar](https://github.com/Ramotion/animated-tab-bar)  | 719 | `C` | 2.67 | **9125** | NA
[Wordpress](https://github.com/wordpress-mobile/) | [wordpress-ios](https://github.com/wordpress-mobile/WordPress-iOS) | 92142  | `C`   | 2.61  | **2322**  | NA
[Hyper](https://github.com/hyperoslo/) | [Spots](https://github.com/hyperoslo/spots) | 7275  | `C`   | 2.55    | **1298**  | 76%
[CosmicMind](https://github.com/CosmicMind)  | [Material](https://github.com/CosmicMind/Material)  | 8266   | `C` | 2.41 | **10044** | NA
[Airbnb](https://github.com/airbnb/) | [Lottie](https://github.com/airbnb/lottie-ios) | 5433 | `C` | 2.36 | **14226** | NA
[MengTo](https://github.com/MengTo) | [Spring](https://github.com/eonist/MengTo/Spring)  | 1612 | `C` | 2.17 | **12380** | NA
[SwiftyJSON](https://github.com/SwiftyJSON/) | [SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON) | 1037  | `D`   | 2.01  | **17172**  | NA
[Uber](https://github.com/uber/) | [UBTokenBar](https://github.com/uber/UBTokenBar) | 513  | `D`   | 1.96  | **105**  | NA
[AudioKit](https://github.com/AudioKit/) | [AudioKit](https://github.com/AudioKit/AudioKit) | 36552  | `E`   | 1.48  | **5621**  | NA

 
 
**LOC**: Lines of code (Says something about the size of the project)  
**Grade**: Corresponds to GPA. Everything over 3.3 Gets an `A` 🥇   
**GPA**: If the code is well structured, Does it avoid code smells. Is the cyclomatic complexity low. Basically says something about the code quality of the codebase. Good code quality means less bugs and less frustration when interacting with the codebase. [codebeat GPA explained](https://hub.codebeat.co/docs/gpa-explained)    
**Stars**: Correlates to how many other developers that find the project interesting / useful and wants to show their support for your OSS effort  
**CodeCov**: Indicates how much of the codebase has Continuous testing

These metrics are important. You should probably avoid adding dependencies that has a GPA grade of C. Simply because they will degrade your project. And the dependencies will struggle with bugs and complexity as time goes on and might even become impossible to work on. Humans have a tendency to pick from big house hold names and brands or whatever is recognized as being popular, it's a quick way to measure quality, but isn't necessarily wise. With Continuous Code Quality services like codebeat, Codacy or SonarCube we can pick better dependencies and learn to code better software. It's not too far fetched to think that at some point in the future GPA wont only apply to the project you work on but also to the dependencies your project relies on. Something like a dependency score. 

### Digital super powers 💪
You can create a digital product on string budget. But in my opinion you can save a lot of money by joining the SaaS eco system and paying for services that will save you time. Definitely bootstrap the MVP and ship the product to market. But once you start to form a company. Suddenly you become dependent on creating a culture that can produce high quality code. At which point it's vital to re-strap and pay for a few services that will give your developers super powers. It could mean paying 25$-100$ monthly for services but it would enable your developer team of 2-3 people to do the job of 5-6 developers. 

My modest personal service/bot budget consists of: Codebeat 20$ month, Github 7$ month, circle CI 39$ a month. I use these service to help maintain the code quality of projects i maintain and also so that I am up to date and have the in-depth knowledge required to operate these services for external clients. Small cost, high impact for both my self and companies I work for. If your more than one developer on a team it's crucial to invest in CI (Continues integration). No more: but it compiles on my machine. In this day and age almost 100% of our work is possible to do asynchronously. And as such, work that your teammates push should not be able to break CI. If it does than it also breaks everyones work and can cause multiple delays and bottle necks. Github currently has CI support for both pull requests and commits. 

In a professional project you should aim to have:  

CI | Vendor | Price  
--- | --- | ---
Continuous quality | [ Codebeat](https://codebeat.co)   | Free* (20$/m)
Continuous building | [Circle CI](https://circleci.com/)   | Free* (39$/m)
Continuous testing | [Codecov](https://codecov.io/)  | Free* (29$/m)
Continuous documentation | [Jazzy](https://github.com/realm/jazzy)   | Free  
Continuous release  | [Fastlane](https://fastlane.tools)  | Free    

`*Free for public OSS (Open-source-software)`

### Subscription life 💸
I know, I know. Subscription life sucks. But time is finite and if you can go faster you can do more in less time. Personally I don't like all these services automatically pulling $$ from my bank account. There might be inconsistencies, or credit card information may be leaked and misused. So to avoid scenarios draining my normal bank account I pay for a prepaid visa card and fill it up every quarter with 3-400$. That way if the card is defrauded I don't need to go through the hoops and legal process of trying to get back any serious amount of money. There is usually a one time fee to fill up prepaid cards, but a few dollars feels totally worth the extra level of protection it offers. It also feels more tangible to fill up the prepaid account with money on a regular basis and gives you the chance to evaluate if a service really worth it?. Sometimes the company you work for are don't want to spend pocket money on SaaS services because it adds to their overall organizational friction or they dont want to come of as careless with company funds. By personally being in charge for paying for the service your team needs, you can move faster and try more services without having to go through too much red tape. Then just take it up with billing after the fact, and explain how instrumental it was in the success of project XYZ, its always easier to ask for something when everyone is on a high note. Using anonymous visa cards also has the added benefit of leaving your personal name out of it. When shit hits the fan and the credit-card processor gets hacked and their entire database winds up on the dark web. At least your name isn't added to the increasing "personal-information-database" that is growing by the day on the dark-web. GDPR doesn't exactly apply to the dark-web. Either that or the card provider sells your info to some shady third-party ad tracking company. You know when your insta gets filled with SaaS commercials that someone sold your info. So to avoid all this, what you do is: Personal Visa -> Cash ->  pre-paid visa -> live a happy life 🏖🍸

