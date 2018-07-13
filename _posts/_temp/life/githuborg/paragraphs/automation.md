### The cost of poor code quality. 
It's rumored that Microsoft is launching a new phone os, which they have spent years developing with a huge team, but its most likley being canceled because the overall code quality is so bad that its beyond saving. 80% is maintenance/code improve 20% is new features. yet many projects think of software as 100% building features. Write about stagnation related to poor code quality.

insert table that highlight code quality 

Sindre sorhus 
Apple example project 
Eonist HybridCamera 
AlamoFire 
SwiftyJason
JohnSundel latest project 

Author  | Project | LOC | Grade | GPA | Stars 
----- | --- | --- | ---| ---
 [CosmicMind](https://github.com/CosmicMind)  | [Material](https://github.com/CosmicMind/Material)  | 8266   | `C` | 2.41 | **10044** 
[Eonist](https://github.com/eonist)  | [HybridCamera](https://github.com/eonist/HybridCamera)  | 756 | `A` | 3.41 | **5**
[Ramotion](https://github.com/Ramotion)  | [animated-tab-bar](https://github.com/Ramotion/animated-tab-bar)  | 719 | `C` | 2.75 | **9125**
[SindreSorhus](https://github.com/SindreSorhus)  | [gifski](https://github.com/SindreSorhus/gifski-app) | 788 | `A` | 3.63 | **2331**
 [Eonist](https://github.com/eonist) | [Element](https://github.com/eonist/Element)  | 5718 | `A` | 3.38 | **815**


**LOC**: Lines of code (Says something about how big the project is)  
**Grade**: Corresponds to GPA. Everything over 3.3 Gets an `A`  
**GPA**: Is the code well structured, Does it avoid code smells etc. Is the cyclomatic complexity low. Basically says something about the code quality of the codebase. Good code quality means less bugs and less frustration when interacting with the codebase.  
**Stars**: Coorelates to how many developers that find the project interesting / useful 

// TODO: add fork count, it says something about people considering using it etc

### Digital tool belt
You can create a digital product on string budget. But in my opinion you can save a lot of money by joining the SaaS eco system and paying for services that will save you time. Definitely bootstrap the MVP and ship the product to market. But once you start to form a company. Suddenly you become dependent on creating a culture that can produce high quality code. At which point it's vital to re-strap and pay for a few services that will give your developers super powers. It could mean paying 25$-100$ monthly for services but it would enable your developer team of 2-3 people to do the job of 5-6 developers. 

My modest personal service/bot budget consists of: Codebeat 20$ month, Github 7$ month, circle CI 39$ a month. I use these service to help maintain the code quality of projects i maintain and also so that I am up to date and have the in-depth knowledge required to operate these services for external clients. Small cost, high impact for both my self and companies I work for. If your more than one developer on a team it's crucial to invest in CI (Continues integration). No more: but it compiles on my machine. In this day and age almost 100% of our work is possible to do asynchronously. And as such, work that your teammates push should not be able to break CI. If it does than it also breaks everyones work and can cause multiple delays and bottle necks. Github currently has CI support for both pull requests and commits. 