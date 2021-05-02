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
- All AutoLayout code is in the swift files so it's easy to do code-reviews on github
- Programatic AutoLayout is version-controllable in a sane way. 🧘
- No more abstract and unreadable .xib syntax
- Many of the biggest apps in the world already use SnapKit. As such your knowledge about it is important for your career.
- Programatic AutoLayout is the industry standard for higher-end apps 👔
- IB layout used to be WYSIWYG. As apps have advanced in complexity WYSIWYG editors simply can't keep up
- No more convoluted AutoLayout constraint error messages, you'll have absolute control over AutoLayout
- As developers we need to ascend to a higher level of UI/UX programming. A place where using ReactiveNative is impossible.
- Whats more readable 4-5 panels with 100s of checkboxes, OR the line: `view.snap(self)`
- InterfaceBuilder crashes xCode if there are too many views 💥
- InterfaceBuilder is really slow when there are many views
- More advance conventions and workflows regarding Interfacebuilder and autolayout quickly becomes non-sensical
- Segues etc are better captured and handled in code.
- InterfaceBuilder is not suited for making fancy responsive UI/UX
- merge conflict increases when you have many people working on one storyboard
- The more programatic AutoLayout you know the easier it will be to live in a IB/Code hybrid world. You cannot survive on InterfaceBuilder alone.
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
- Depends on third party lib to really write code efficiently (Vanilla AutoLayout code is not very maintainable, as its too verbose)
- Apples own programatic AutoLayout is verbose and powerful but more code means less simple to maintain
- Takes slightly more time to initially setup than IB.
- IB AutoLayout is ok for beginners. Or really simple apps. Or trying out an idea.
- "Birds eye view" of views are cool. But to make IB + AL work you have to create many storyboards, and the effect is lost.
- IB is great for simple looking vanilla apps. ✌️

#### Gotchas when using programatic AutoLayout
- Use loadView instead of viewDidLoad for creating views with constraints
This example uses the anchor syntax and not the older constraint syntax
```swift
override func loadView() {
     super.loadView()

     let testView = UIView(frame: .zero) // Always set the frame to .zero when using custom autolayout code
     testView.translatesAutoresizingMaskIntoConstraints = false // Always set this to false if you want to set custom AutoLayout code
     self.view.addSubview(testView)
     NSLayoutConstraint.activate([
         testView.widthAnchor.constraint(equalToConstant: 64),
         testView.widthAnchor.constraint(equalTo: testView.heightAnchor),
         testView.centerXAnchor.constraint(equalTo: self.view.centerXAnchor, constant: 0),
         testView.centerYAnchor.constraint(equalTo: self.view.centerYAnchor, multiplier: 1)
     ])
     self.testView = testView
 }
```

### Simple NSLayoutConstraint example:

Conventional programatic AutoLayout ( NSLayoutConstraint Style )
```swift
let horizontalConstraint = NSLayoutConstraint(item: childView, attribute: .centerX, relatedBy: .equal, toItem: parentView, attribute: .centerX, multiplier: 1.0, constant: 0)
parentView.addConstraints([horizontalConstraint])
```

Equivalent to Anchor-code: ( Anchor Style)
```swift
let horizontalConstraint = childView.centerXAnchor.constraint(equalTo: parentView.centerXAnchor)
parentView.addConstraints([horizontalConstraint])
```

Which is also equivalent to: (  Anchor Style + immediate activation)
```swift
childView.centerXAnchor.constraint(equalTo: parentView.centerXAnchor).isActive = true// We are activating the constraint right after it is returned.
```

You can also bulk activate anchor constraints:
```swift
square.translatesAutoresizingMaskIntoConstraints = false
NSLayoutConstraint.activate([
    square.widthAnchor.constraint(equalToConstant: 64),
    square.heightAnchor.constraint(equalToConstant: 64),
    square.centerXAnchor.constraint(equalTo: self.view.centerXAnchor),
    square.centerYAnchor.constraint(equalTo: self.view.centerYAnchor),
])
```
Addendum:
- Anchor style is the preferred method over NSLayoutConstraint Style, however it is only available from iOS 9, so if you are supporting iOS 8 then you should still use NSLayoutConstraint Style.
- Anchor style has these types of anchors: `topAnchor,bottomAnchor,leadingAnchor,trailingAnchor,widthAnchor,heightAnchor`
- Conceptually speaking: The NSLayoutAnchor class is a factory class for creating NSLayoutConstraint objects using a fluent API. Use these constraints to programatically define your layout using Auto Layout.


### Pinning to edges: (Using older Constraint syntax)

```swift
let newView =UILabel()
    self.view.addSubview(newView)
    newView.translatesAutoresizingMaskIntoConstraints = false
    let horizontalConstraint = NSLayoutConstraint(item: newView, attribute: NSLayoutAttribute.centerX, relatedBy: NSLayoutRelation.equal, toItem: view, attribute: NSLayoutAttribute.centerX, multiplier: 1, constant: 0)
    let verticalConstraint = NSLayoutConstraint(item: newView, attribute: NSLayoutAttribute.centerY, relatedBy: NSLayoutRelation.equal, toItem: view, attribute: NSLayoutAttribute.centerY, multiplier: 1, constant: 0)
    let widthConstraint = NSLayoutConstraint(item: newView, attribute: NSLayoutAttribute.width, relatedBy: NSLayoutRelation.equal, toItem: nil, attribute: NSLayoutAttribute.notAnAttribute, multiplier: 1, constant: 100)
    let heightConstraint = NSLayoutConstraint(item: newView, attribute: NSLayoutAttribute.height, relatedBy: NSLayoutRelation.equal, toItem: nil, attribute: NSLayoutAttribute.notAnAttribute, multiplier: 1, constant: 100)
    NSLayoutConstraint.activate([horizontalConstraint, verticalConstraint, widthConstraint, heightConstraint])
}
```

#### Dodging the infamous "Notch" in iPhoneX
Alternatively you can set the the root view in didLayoutSubviews, with a bit off offset
```swift
someView.topAnchor.constraint(equalTo: self.view.safeAreaLayoutGuide.topAnchor),
someView.leadingAnchor.constraint(equalTo: self.view.safeAreaLayoutGuide.leadingAnchor),
someView.trailingAnchor.constraint(equalTo: self.view.safeAreaLayoutGuide.trailingAnchor),
someView.bottomAnchor.constraint(equalTo: self.view.safeAreaLayoutGuide.bottomAnchor),
```

### Gotchas:
- If you want to update layers with AutoLayout: you can put this update code in: `viewDidLayoutSubviews` or `override var bounds: CGRect { didSet { /*Add custom layer code here*/ } }`

### Gotchas when animating:
- Use standard UIView animation with layoutIfNeeded
- Always deactivate constraints first
- Store constraints in the instances, for re-creating after deactivate

### Using a LayoutGuide:  
Layout guides provides a lightweight method for encapsulating part of your layout. Note that this technique only affects how Auto Layout interacts with the encapsulated views. It does not change the view hierarchy in any way.
```swift
let container = UILayoutGuide()
view.addLayoutGuide(container)

// Set interior constraints
label.lastBaselineAnchor.constraintEqualToAnchor(textField.lastBaselineAnchor).active = true
label.leadingAnchor.constraintEqualToAnchor(container.leadingAnchor).active = true
textField.leadingAnchor.constraintEqualToAnchor(label.trailingAnchor, constant: 8.0).active = true
textField.trailingAnchor.constraintEqualToAnchor(container.trailingAnchor).active = true
textField.topAnchor.constraintEqualToAnchor(container.topAnchor).active = true
textField.bottomAnchor.constraintEqualToAnchor(container.bottomAnchor).active = true

// Set exterior constraints
// The contents of the container can be treated as a black box
let margins = view.layoutMarginsGuide

container.leadingAnchor.constraintEqualToAnchor(margins.leadingAnchor).active = true
container.trailingAnchor.constraintEqualToAnchor(margins.trailingAnchor).active = true
container.topAnchor.constraintEqualToAnchor(topLayoutGuide.bottomAnchor, constant: 20.0).active = true
```

### Adding margins:

```swift
self.view.directionalLayoutMargins = NSDirectionalEdgeInsets(top: self.view.directionalLayoutMargins.top,
                                                             leading: 16,
                                                             bottom: self.view.directionalLayoutMargins.bottom,
                                                             trailing: 64)
//For when layoutMargins are less than system minimum be sure to apply:
self.viewRespectsSystemMinimumLayoutMargins = false
self.view.directionalLayoutMargins = NSDirectionalEdgeInsets(top: self.view.directionalLayoutMargins.top,
                                                            leading: 0,
                                                            bottom: self.view.directionalLayoutMargins.bottom,
                                                            trailing: 8)  


```
The specification of autolayout constraints taking into account margins is possible with the layout guide available through the layoutMarginsGuide property and also using the preservesSuperviewLayoutMargins                                                        


### Hugging and resistance
See blog post named: Compression, resistance, priority when using AutoLayout


### resources:

https://www.hackingwithswift.com/articles/140/the-auto-layout-cheat-sheet

dynamic height lable: https://medium.com/@aainajain/dynamic-label-using-auto-layout-with-maximum-height-b3211a446de6

### When using autolayout on NSIMageView: 

```swift
nsImageView.imageScaling = .scaleAxesIndependently
```
