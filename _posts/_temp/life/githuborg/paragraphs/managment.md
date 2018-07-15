
### Github is for everyone
Maybe someone on your team that isn't a developer discovers a bug. Giving non-developers access to github repos enables them to: 

1. Check if a similar issue is already present, if not then [open a github issue](https://github.com/sindresorhus/gifski-app/issues/40). 
2. Ask the core dev(s) on the project, with `@` tagging, if it can be fixed. 
3. Then the developers on the project will investigate the issue discussing urgency / priority / complexity 
4. Finally assigning a priority to the issue and then with `@ tagging politely ask if someone has time to solve it. 

If the issues piles up and there are tens or hundreds of issues, then it's probably a good sign that the codebase is lacking code quality or there is lack of allocated time for maintenance of the codebase. Once an issue is fixed, the issue is closed and a link to the Pull-requst that fixed the problem is added to the bottom of the issue. Keeping a tight ship is make it or break it when your building complex software involving many people / teams / dependencies. The point of all this is that the problem is presented front and center, the issue finder gets credit for finding the issue, bonus points for adding a description about what led to the error. Who ever investigates the issue and finds additional information about the issue gets credit for time spent. And finally who ever solved the problem gets commit credit. And with githubs new team commit. Everyone that was involved should be credited authors of the commit.

Let everyone be involved in making the product, its infinitely more rewarding helping building something if it's noticed and the effort is recorded and timestamped in the commit history of the project. In my opinion everyone on the team should have read and write access to all projects in the company github organization. If someone comes up with a feature that should be added then this can be added as a feature/enhancement request in the issue tracker. Tagging the issue `Help wanted` `Low/High priority`, `Question`. Adding feature as issues also has the added effect that people cant request the idea twice, because it would already be added and closed. 

Issues can have link pointers to PR's that solve them, PR's can have links to wiki posts that explain the further rational behind the Pull-Requst. Wikipages can have links to design assets in other repositories in the github organization etc. Or API documentation. Everything stored as markdown which is easily editable by everyone with full commit history of who added/edited what. 

### All inclusive 
Everything that is relevant in the project. API documentation, readme files, UX / Design assets. Because everything on github allows team-mates to comment and discuss changes and additions. Involving the coders in the design process of features and products are crucial when it comes to calculating the likelihood of its accomplishable or not. Not involving the person that is finally going to be making the product is a recipe for disaster both in terms of motivation and in terms of defining expectations and timeframe of completion. A workflow I enjoy on github is storing .gif .jpg and even .svg directly in a repo and using urls similar to `https://rawgit.com/repo-name/img/master/image.jpg` and svgs via rawsvg: `https://rawgit.com/stylekit/img/master/FileWatcher.svg`

### Personal career 👔
I spend 100% of my working hours on github, call me a native githubber. I manage my career on github as oppose to linkdin. I get most of my freelance inquiries and job opportunities as a result of my visibility on github. Frankly github is what puts bread on my table. 
Just like how maintaining a relevant dribbble profile is crucial for a graphic designers and having songs on soundcloud is vital for aspiring musicians. Is your work impactful? Do you write with the highest standard of the industry? Education, work experience and network still counts, to a degree, but it's probably only a matter of time before unbiased CI quality bots are applied to student exams. Imagine students getting a grade seconds after they "commit" their exam. Ai bots analyzing meaning, grammar, "impact-fullness" and degree of plagiarism in nano seconds. [Like Aaron Lichtman is working on](https://github.com/alichtman) 

### Feature creep 
Management has a tendency to ask for additional features after features are completed by developers. Because surly after something is done the developer doesn't have anything to do. Unfortunately thats not how software building works. Piling on features only leads to complexity and bad code quality, complexity that has to be maintained. After x-amounts of features the developer responsible will have to use more and more of his time to maintain the complexity. 

### Leaving a paper trail
Managing the product development on github is crucial because it highlights what everyone is working on. On the surface make feature X and Y may seem trivial but to accomplish it issued must be solved, dependencies has to be tested and updated. CI tests have to be written, Issues must be discussed and Documentation must be written. It may very well be that in the end it takes 4 weeks instead of 2. But the crucial part is that there is a paper-trail of why and how it took twice the time that was estimated. Interacting with the complex nature of software projects is not the same as estimating more linear tasks such as painting a wall or installing a new kitchen. Sure you can always power though it and build something that resembles the specs, but not with out corrupting the overall product / code quality. Do this enough times and your left with an intertwined pile of unusable spaghetti code. That is impossible to improve or even roll back because dependencies moved on etc. By doing the entire production managment on github. Managment has full control over who does what. Github has a myriad of activity logs front and center. Both in terms of the project and individual developers that work on the project. 

<img width="644" alt="img" src="https://rawgit.com/stylekit/img/master/Screen Shot 2018-07-15 at 19.31.32.png">

### Premise of the developer
If your going to run a software company it's wise to run it on the premise of the developers that are building it and the platform where it’s being built. Managers love google suite, but developers usually don’t. Developers also usually has less down time to spare than managers and support staff. Developers usually operate at 100% cognition where as managers often has a lot of down time between meetings and organizational work.


### Simplifying complexity
Keeping documentation where the projects are built. Instead of putting documents on google, api instructions on slack , UX pdfs on a google drive, and issues in basecam. One should rather collect it in the place where the digital product is being built. Github caters to all the needs described above. Documents can be collaboratively written in the wiki section, complete with commit history etc. You can even update wikis from the comfort of your favorit text editors as the entire wiki section is a .git repo in it self. Issues should be documented and discussed in the issue section. Design assets can be version controlled in it's own repo through [kaktus](https://kactus.io/)  instead of in emails / dropbox. The great thing about collaborating on github is that no resolution is lost, all efforts are version controlled. It's also the industry standard when it comes to building software so everyone (should) know it as if it was the back of their hand. Learning Markdown is also vital. Learning markdown takes a few minutes and is a lot of fun to master ;) 




