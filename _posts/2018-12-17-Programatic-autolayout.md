My notes on the pros and cons of programatic AutoLayout<!--more-->

## Why choose programatic AutoLayout:

#### Pro's:
- Easier to maintain than InterfaceBuilder
- Easy to debug once something goes wrong (as appose to Interface Builder)
- Faster to cognitively grasp what AutoLayout does. IB has too many parameters all over the place
- Easier to setup for complex rich interactivity / animation (This is almost impossible in IB)
- Less parameters for more impact (Use of one-liners in snapKit, TinyConstraints and Spatial)
- The future is more and more Advance UI/UX and less boilerplate generic looks
- The parameters are in pure code so it's easy to comment edge cases and gotchas (Impossible in IB)
- All autolayout code is in the swift files so it's easy to do code-reviews on github
- Programatic AutoLayout is version-controllable in a sane way.
- No more abstract and unreadable .xib syntax
- Many of the biggest apps in the world already use SnapKit. As such your knowledge about it is important for your career.
- Programatic AutoLayout is the industry standard for higher-end apps
- IB layout used to be WYSIWYG. As apps have advanced in complexity WYSIWYG editors simply can't keep up
- No more convoluted AutoLayout constraint error messages, you'll have absolute control over AutoLayout
- As developers we need to ascend to a higher level of UI/UX programming. A place where using ReactiveNative is impossible.
- Whats more readable 4-5 panels with 100s of checkboxes, OR the line: `view.snap(self)`

#### Con's
- Slight learning-curve for iOS beginners
- Depends on third party lib to really write code efficiently
- Apples own programatic AutoLayout is verbose and powerful but more code means less simple to maintain
- Takes slightly more time to initially setup than IB.
- IB AutoLayout is ok for beginners. Or really simple apps. Or trying out an idea.
