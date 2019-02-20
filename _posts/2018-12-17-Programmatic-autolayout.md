Pros and cons of programatic AutoLayout<!--more-->

## Why choose programatic AutoLayout:

#### Pro's:
- Easier to maintain than InterfaceBuilder 👌
- Easy to debug once something goes wrong (as appose to Interface Builder)
- Faster to cognitively grasp what AutoLayout does. IB has too many parameters all over the place
- Easier to setup for complex rich interactivity / animation (This is almost impossible in IB)
- Less parameters for more impact (Use of one-liners in snapKit, TinyConstraints and Spatial)
- The future is more and more Advance UI/UX and less boilerplate generic looks
- The parameters are in pure code so it's easy to comment edge cases and gotchas (Impossible in IB)
- All autolayout code is in the swift files so it's easy to do code-reviews on github
- Programatic AutoLayout is version-controllable in a sane way. 🧘
- No more abstract and unreadable .xib syntax
- Many of the biggest apps in the world already use SnapKit. As such your knowledge about it is important for your career.
- Programatic AutoLayout is the industry standard for higher-end apps 👔
- IB layout used to be WYSIWYG. As apps have advanced in complexity WYSIWYG editors simply can't keep up
- No more convoluted AutoLayout constraint error messages, you'll have absolute control over AutoLayout
- As developers we need to ascend to a higher level of UI/UX programming. A place where using ReactiveNative is impossible.
- Whats more readable 4-5 panels with 100s of checkboxes, OR the line: `view.snap(self)`
- InterfaceBuilder crashes xcode if there are too many views 💥
- InterfaceBuilder is really slow when there are many views
- More advance conventions and workflows regarding Interfacebuilder and autolayout quickly becomes non-sensical
- Segues etc are better captured and handled in code.
- InterfaceBuilder is not suited for making fancy responsive UI/UX
- merge conflict increases when you have many people working on one storyboard
- The more programatic AutoLaytout you know the easier it will be to live in a IB/Code hybrid world. You cannot survive on InterfaceBuilder alone.
- `Unable to simultaneously satisfy constraints, will attempt to recover by breaking constraint` 💀
- The comments surrounding the programatic AutoLayout code is crusial, as we can spend a lot less time deriving the context the original problem was solved. If this statement sounds unambigiouse then please read my article on [commenting and its effect on cognition]()
- ios 9 edge cases needs documentation
- When subContent sets parent constraints, things gets complicated and commenting and subclassing is needed to keep the code readable and maintainable
- My final argument is that Programatic AutoLayout enables you to have a steel grip over your UI / UX. No more half baked transitions that are fair enough. Own your design. Make it shine ✨
- Storyboards are more prone to version conflicts due to their complex XML structure. This makes merging much harder than with code.
- It's easier to structure and reuse views in code, thereby keeping your codebase DRY.
- All information is in one place. In Interface Builder you have to click through all the inspectors to find what you're looking for.
- Storyboards introduce coupling between your code and UI, which can lead to crashes e.g. when an outlet or action is not set up correctly. These issues are not detected by the compiler.

#### Con's
- Slight learning-curve for iOS beginners
- Depends on third party lib to really write code efficiently (Vanilla Autolayout code is not very maintainable, as its too verbose)
- Apples own programatic AutoLayout is verbose and powerful but more code means less simple to maintain
- Takes slightly more time to initially setup than IB.
- IB AutoLayout is ok for beginners. Or really simple apps. Or trying out an idea.
- Birds eyeview of views are cool. But to make IB + AL work you have to create many storyboards, and the effect is lost.
- IB is great for simple looking vanilla apps. ✌️
