My notes on making a horizontal tag bar<!--more-->

### Resources
- Scrollable uistackview: https://useyourloaf.com/blog/scrolling-stack-views/ 👈
- Flowlayout: https://github.com/eonist/flowlayout
- Tag list tidbits (IB) : https://github.com/ElaWorkshop/TagListView
- Search tokens / tags: https://github.com/whitesmith/WSTagsField
- Scrollable StackView: https://github.com/gurhub/ScrollableStackView
- Scrollable stackview with IB: https://medium.com/@mufakkharulislamnayem/scrolling-in-a-horizontal-uistackview-with-storyboard-e02a9aab555b
- SwiftUI has native scrollable stackviews: https://developer.apple.com/documentation/swiftui/creating-performant-scrollable-stacks
- Nice collection of info regarding spacing in stackview: https://uynguyen.github.io/2020/07/18/iOS-Introducing-Stack-Views/ with github: https://github.com/uynguyen/UIStackView
- Discussion on horizontal uicollectionview: https://stackoverflow.com/questions/19301762/uicollectionview-horizontal-scroll-horizontal-layout
- programatic uicollectionview: https://stackoverflow.com/questions/17856055/creating-a-uicollectionview-programmatically
- Complete uicollectionview example: https://www.appsdeveloperblog.com/create-uicollectionview-programmatically-in-swift/
- Spotify like layout (uicollectionview) https://www.freecodecamp.org/news/autolayout-programmatically-spotify-clone-in-swift/
- SwiftUI ScrollView + HStack: https://medium.com/@max.codes/swiftui-collection-view-with-lists-1fa9f97a1f8

### Gotchas:
- The horizontal tag bar might need to be inside the vertical scrolling table. Dual uicollectionviews support this. But unsure if stackview supports this etc
- UICollectionView has scrollToItem UIStackView needs to have this built custom. here is uicollectionview api: self.collectionView.scrollToItem(at: IndexPath(item: 12, section: 0), at: [.centeredVertically, .centeredHorizontally], animated: true)
- Making the scrollToVisible could be tricky to make so that it scrolls into view from correct side. Either left, right, bottom, top. Consider making: scrollToNearestVisiblePosition?

### Scope:
- selectedIndex 🔢
- Horizontally scrollable ↔️
- isSelected sets item in highlighted state 👇
- Should have elastic animation (UIScrollView) 👌
- Should have showSelectedIndex (makes selected item visible) (for end and begining items)
- Work in DarkMode and light mode (use same look as desktop?) 🌓
- Make a generic ScrollingStackView as baseclass that you can customize to custom look and feel 🛠
- Highlight should animate into place / size (reach goal) see notes app video for example ✨

### Todo:
- Do more research on dribbble ✅
- Do more research on google (horizontal stackview scrollable, etc etc) ✨
- Look through FlowLayout 👈
- Look through resource links ✅
- Write pseudo code ✅
- Find instagram post on horizontal tag bar ✅

### Horizontal UICollectionView
```swift
var myArray = [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18]
let rows = 3
let columnsInFirstPage = 5
// calculate number of columns needed to display all items
var columns: Int { return myArray.count<=columnsInFirstPage ? myArray.count : myArray.count > rows*columnsInFirstPage ? (myArray.count-1)/rows + 1 : columnsInFirstPage }
override func collectionView(collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {        
    return columns*rows
}
override func collectionView(collectionView: UICollectionView, cellForItemAtIndexPath indexPath: NSIndexPath) -> UICollectionViewCell {
    let cell = collectionView.dequeueReusableCellWithReuseIdentifier("Cell", forIndexPath: indexPath)
    //These three lines will convert the index to a new index that will simulate the collection view as if it was being filled horizontally
    let i = indexPath.item / rows
    let j = indexPath.item % rows         
    let item = j*columns+i

    guard item < myArray.count else {
        //If item is not in myArray range then return an empty hidden cell in order to continue the layout
        cell.hidden = true
        return cell
    }
    cell.hidden = false
    //Rest of your cell setup, Now to access your data You need to use the new "item" instead of "indexPath.item"
    //like: cell.myLabel.text = "\(myArray[item])"
    return cell
}
```

### Horizontal scrollable UIStackView:
- Ref: https://stackoverflow.com/a/54386559/5389500
- Note: might not support go to item scroll etc, UICollectionView could be prefered in this case (this has support: https://github.com/gurhub/ScrollableStackView)

```swift
func createHorizontalStackViewsWithScroll() {
    self.view.addSubview(stackScrollView)
    stackScrollView.translatesAutoresizingMaskIntoConstraints = false
    stackScrollView.heightAnchor.constraint(equalToConstant: 85).isActive = true
    stackScrollView.leadingAnchor.constraint(equalTo: self.view.leadingAnchor).isActive = true
    stackScrollView.trailingAnchor.constraint(equalTo: self.view.trailingAnchor).isActive = true
    stackScrollView.bottomAnchor.constraint(equalTo: visualEffectViews.topAnchor).isActive = true
    stackScrollView.addSubview(stackView)

    stackView.translatesAutoresizingMaskIntoConstraints = false
    stackView.topAnchor.constraint(equalTo: stackScrollView.topAnchor).isActive = true
    stackView.leadingAnchor.constraint(equalTo: stackScrollView.leadingAnchor).isActive = true
    stackView.trailingAnchor.constraint(equalTo: stackScrollView.trailingAnchor).isActive = true
    stackView.bottomAnchor.constraint(equalTo: stackScrollView.bottomAnchor).isActive = true
    stackView.heightAnchor.constraint(equalTo: stackScrollView.heightAnchor).isActive = true

    stackView.distribution = .equalSpacing
    stackView.spacing = 5
    stackView.axis = .horizontal
    stackView.alignment = .fill

    for i in 0 ..< images.count {
        let photoView = UIButton.init(frame: CGRect(x: 0, y: 0, width: 85, height: 85))
        // set button image
        photoView.translatesAutoresizingMaskIntoConstraints = false
        photoView.heightAnchor.constraint(equalToConstant: photoView.frame.height).isActive = true
        photoView.widthAnchor.constraint(equalToConstant: photoView.frame.width).isActive = true

        stackView.addArrangedSubview(photoView)
    }
    stackView.setNeedsLayout()
}
```

### Another scrollable stackview example:
```swift
import UIKit
import PlaygroundSupport

class TestViewController : UIViewController {
    let scrollView: UIScrollView = {
        let v = UIScrollView()
        v.translatesAutoresizingMaskIntoConstraints = false
        v.backgroundColor = .cyan
        return v
    }()
    let stackView : UIStackView = {
        let v = UIStackView()
        v.translatesAutoresizingMaskIntoConstraints = false
        v.axis = .horizontal
        v.distribution = .equalSpacing
        v.spacing = 10.0
        return v
    }()
    override func viewDidLoad() {
        super.viewDidLoad()

        // add the scroll view to self.view
        self.view.addSubview(scrollView)

        // constrain the scroll view to 8-pts on each side
        scrollView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 8.0).isActive = true
        scrollView.topAnchor.constraint(equalTo: view.topAnchor, constant: 8.0).isActive = true
        scrollView.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -8.0).isActive = true
        scrollView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -8.0).isActive = true


        // add the stack view to the scroll view
        scrollView.addSubview(stackView)

        // constrain the stackview view to 8-pts on each side
        //   this *also* controls the .contentSize of the scrollview
        stackView.leftAnchor.constraint(equalTo: scrollView.leftAnchor, constant: 8.0).isActive = true
        stackView.topAnchor.constraint(equalTo: scrollView.topAnchor, constant: 8.0).isActive = true
        stackView.rightAnchor.constraint(equalTo: scrollView.rightAnchor, constant: -8.0).isActive = true
        stackView.bottomAnchor.constraint(equalTo: scrollView.bottomAnchor, constant: -8.0).isActive = true

        // add ten buttons to the stack view
        for i in 1...10 {

            let b = UIButton()
            b.translatesAutoresizingMaskIntoConstraints = false
            b.setTitle("Button \(i)", for: .normal)
            b.backgroundColor = .blue
            stackView.addArrangedSubview(b)

        }

    }

}

let vc = TestViewController()
vc.view.backgroundColor = .yellow
PlaygroundPage.current.liveView = vc
```

### ScrollableStackView
- From: https://github.com/gurhub/ScrollableStackView

```swift
import ScrollableStackView

var scrollable = ScrollableStackView(frame: view.frame)
view.addSubview(scrollable)

// add your views with
let rectangle = UIView(frame: CGRect(x: 0, y: 0, width: 100, height: 55))
rectangle.backgroundColor = UIColor.blue
scrollable.stackView.addArrangedSubview(rectangle)
// ...

// you can set margins directly on the stackview
scrollable.stackView.layoutMargins = UIEdgeInsets(top: 8, left: 8, bottom: 8, right: 8)
scrollable.stackView.isLayoutMarginsRelativeArrangement = true
```
Source:
```swift
import UIKit

@IBDesignable
public class ScrollableStackView: UIView {

    fileprivate var didSetupConstraints = false
    @IBInspectable open var spacing: CGFloat = 8
    open var durationForAnimations:TimeInterval = 1.45

	public lazy var scrollView: UIScrollView = {
		let instance = UIScrollView(frame: CGRect.zero)
		instance.translatesAutoresizingMaskIntoConstraints = false
		instance.layoutMargins = .zero
		return instance
	}()

	public lazy var stackView: UIStackView = {
		let instance = UIStackView(frame: CGRect.zero)
		instance.translatesAutoresizingMaskIntoConstraints = false
		instance.axis = .vertical
		instance.spacing = self.spacing
		instance.distribution = .equalSpacing
		return instance
	}()

    //MARK: View life cycle
    override public func didMoveToSuperview() {
        super.didMoveToSuperview()

        setupUI()
    }

    //MARK: UI
    func setupUI() {
        translatesAutoresizingMaskIntoConstraints = false
        clipsToBounds = true

		addSubview(scrollView)
		scrollView.addSubview(stackView)

        setNeedsUpdateConstraints() // Bootstrap auto layout
    }

    // Scrolls to item at index
    public func scrollToItem(index: Int) {
        if stackView.arrangedSubviews.count > 0 {
            let view = stackView.arrangedSubviews[index]

            UIView.animate(withDuration: durationForAnimations, animations: {
                self.scrollView.setContentOffset(CGPoint(x: 0, y:view.frame.origin.y), animated: true)
            })
        }
    }

    // Used to scroll till the end of scrollview
    public func scrollToBottom() {
        if stackView.arrangedSubviews.count > 0 {
            UIView.animate(withDuration: durationForAnimations, animations: {
                self.scrollView.scrollToBottom(true)
            })
        }
    }

    // Scrolls to top of scrollable area
    public func scrollToTop() {
        if stackView.arrangedSubviews.count > 0 {
            UIView.animate(withDuration: durationForAnimations, animations: {
                self.scrollView.setContentOffset(CGPoint(x: 0, y:0), animated: true)
            })
        }
    }

    override public func updateConstraints() {
        super.updateConstraints()

        if !didSetupConstraints {
			scrollView.topAnchor.constraint(equalTo: self.topAnchor).isActive = true
			scrollView.leadingAnchor.constraint(equalTo: self.leadingAnchor).isActive = true
			scrollView.trailingAnchor.constraint(equalTo: self.trailingAnchor).isActive = true
			scrollView.bottomAnchor.constraint(equalTo: self.bottomAnchor).isActive = true

			stackView.topAnchor.constraint(equalTo: scrollView.topAnchor).isActive = true
			stackView.leadingAnchor.constraint(equalTo: scrollView.leadingAnchor).isActive = true
			stackView.trailingAnchor.constraint(equalTo: scrollView.trailingAnchor).isActive = true
			stackView.bottomAnchor.constraint(equalTo: scrollView.bottomAnchor).isActive = true

			// Set the width of the stack view to the width of the scroll view for vertical scrolling
			stackView.widthAnchor.constraint(equalTo: scrollView.widthAnchor).isActive = true

            didSetupConstraints = true
        }
    }
}

// Used to scroll till the end of scrollview
extension UIScrollView {
    func scrollToBottom(_ animated: Bool) {
        if self.contentSize.height < self.bounds.size.height { return }
        let bottomOffset = CGPoint(x: 0, y: self.contentSize.height - self.bounds.size.height)
        self.setContentOffset(bottomOffset, animated: animated)
    }
}
```

### Another simple scrollable stackview:

```swift
import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        view.addSubview(scrollView)
        scrollView.addSubview(scrollViewContainer)
        scrollViewContainer.addArrangedSubview(redView)
        scrollViewContainer.addArrangedSubview(blueView)
        scrollViewContainer.addArrangedSubview(greenView)

        scrollView.leadingAnchor.constraint(equalTo: view.leadingAnchor).isActive = true
        scrollView.trailingAnchor.constraint(equalTo: view.trailingAnchor).isActive = true
        scrollView.topAnchor.constraint(equalTo: view.topAnchor).isActive = true
        scrollView.bottomAnchor.constraint(equalTo: view.bottomAnchor).isActive = true

        scrollViewContainer.leadingAnchor.constraint(equalTo: scrollView.leadingAnchor).isActive = true
        scrollViewContainer.trailingAnchor.constraint(equalTo: scrollView.trailingAnchor).isActive = true
        scrollViewContainer.topAnchor.constraint(equalTo: scrollView.topAnchor).isActive = true
        scrollViewContainer.bottomAnchor.constraint(equalTo: scrollView.bottomAnchor).isActive = true
        // this is important for scrolling
        scrollViewContainer.widthAnchor.constraint(equalTo: scrollView.widthAnchor).isActive = true
    }

    let scrollView: UIScrollView = {
        let scrollView = UIScrollView()

        scrollView.translatesAutoresizingMaskIntoConstraints = false
        return scrollView
    }()

    let scrollViewContainer: UIStackView = {
        let view = UIStackView()

        view.axis = .vertical
        view.spacing = 10

        view.translatesAutoresizingMaskIntoConstraints = false
        return view
    }()

    let redView: UIView = {
        let view = UIView()
        view.heightAnchor.constraint(equalToConstant: 500).isActive = true
        view.backgroundColor = .red
        return view
    }()

    let blueView: UIView = {
        let view = UIView()
        view.heightAnchor.constraint(equalToConstant: 200).isActive = true
        view.backgroundColor = .blue
        return view
    }()

    let greenView: UIView = {
        let view = UIView()
        view.heightAnchor.constraint(equalToConstant: 1200).isActive = true
        view.backgroundColor = .green
        return view
    }()
}
```

### ScrollableStackView class example:
```swift
class ViewController: UIViewController {
	let stackView = ScrollingStackView()
	…
	override func viewDidLoad() {
		super.viewDidLoad()

		view.embed(stackView)
		setupStackView()
	}
	…
	func setupStackView() {
	    stackView.spacing = 10.0

	    for i in 1..<30 {
	        let label = UILabel()
	        label.text = "Hello, this is label nº \(i)"
	        stackView.addArrangedSubview(label)
	    }
	}
```
- explination: https://sintraworks.github.io/swift/uikit/2018/12/24/scrolling-stackview.html
- Source:
```swift
// from https://gist.github.com/SintraWorks/d6d692a09d9a340517529d139894f6c9
// let stackView = ScrollingStackView(axis: .horizontal).
import UIKit

class ScrollingStackView: UIScrollView {
    private var stackView = UIStackView()
    private var axisConstraint: NSLayoutConstraint?

    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
        commonInit(axis: .vertical)
    }

    override init(frame: CGRect) {
        super.init(frame: frame)
        commonInit(axis: .vertical)
    }

    init(axis: NSLayoutConstraint.Axis = .vertical) {
        super.init(frame: CGRect.zero)
        commonInit(axis: axis)
    }

    convenience init(arrangedSubviews: [UIView], axis: NSLayoutConstraint.Axis = .vertical) {
        self.init(axis: axis)
        for subview in arrangedSubviews {
            stackView.addArrangedSubview(subview)
        }
    }

    private func commonInit(axis: NSLayoutConstraint.Axis) {
        embed(stackView)
        self.axis = axis
    }
}

// MARK: - Pass-throughs to UIStackView
// MARK: Managing arranged subviews
extension ScrollingStackView {
  func addArrangedSubview(_ view: UIView) {
    stackView.addArrangedSubview(view)
  }

  var arrangedSubviews: [UIView] {
    return stackView.arrangedSubviews
  }

  func insertArrangedSubview(_ view: UIView, at stackIndex: Int) {
    stackView.insertArrangedSubview(view, at: stackIndex)
  }

  func removeArrangedSubview(_ view: UIView) {
    stackView.removeArrangedSubview(view)
  }
}

// MARK: Configuring the layout
extension ScrollingStackView {
  var alignment: UIStackView.Alignment {
    get { return stackView.alignment }
    set { stackView.alignment = newValue }
  }

  var axis: NSLayoutConstraint.Axis {
    get { return stackView.axis }
    set {
      axisConstraint?.isActive = false // deactivate existing constraint, if any
      switch newValue as NSLayoutConstraint.Axis {
      case .vertical:
        axisConstraint = stackView.widthAnchor.constraint(equalTo: self.widthAnchor)
      case .horizontal:
        axisConstraint = stackView.heightAnchor.constraint(equalTo: self.heightAnchor)
      }
      axisConstraint?.isActive = true // activate new constraint
      stackView.axis = newValue
    }
  }

  var isBaselineRelativeArrangement: Bool {
    get { return stackView.isBaselineRelativeArrangement }
    set { stackView.isBaselineRelativeArrangement = newValue }
  }

  var distribution: UIStackView.Distribution {
    get { return stackView.distribution }
    set { stackView.distribution = newValue }
  }

  var isLayoutMarginsRelativeArrangement: Bool {
    get { return stackView.isLayoutMarginsRelativeArrangement }
    set { stackView.isLayoutMarginsRelativeArrangement = newValue }
  }

  var spacing: CGFloat {
    get { return stackView.spacing }
    set { stackView.spacing = newValue }
  }
}

// MARK: Adding space between items
@available(iOS 11.0, *)
extension ScrollingStackView {
  func customSpacing(after arrangedSubview: UIView) -> CGFloat {
    return stackView.customSpacing(after: arrangedSubview)
  }

  func setCustomSpacing(_ spacing: CGFloat, after arrangedSubview: UIView) {
      stackView.setCustomSpacing(spacing, after: arrangedSubview)
  }

  class var spacingUseDefault: CGFloat {
    return UIStackView.spacingUseDefault
  }

  class var spacingUseSystem: CGFloat {
    return UIStackView.spacingUseSystem
  }
}

// MARK: - UIView overrides
extension ScrollingStackView {
    override var layoutMargins: UIEdgeInsets {
        get { return stackView.layoutMargins }
        set { stackView.layoutMargins = newValue }
    }

    override  var directionalLayoutMargins: NSDirectionalEdgeInsets {
        get { return stackView.directionalLayoutMargins }
        set { stackView.directionalLayoutMargins = newValue }
    }
}

extension UIView {
    func embed(_ child: UIView) {
        child.translatesAutoresizingMaskIntoConstraints = false
        addSubview(child)
        let constraints = [
            child.leftAnchor.constraint(equalTo: self.leftAnchor),
            child.rightAnchor.constraint(equalTo: self.rightAnchor),
            child.topAnchor.constraint(equalTo: self.topAnchor),
            child.bottomAnchor.constraint(equalTo: self.bottomAnchor),
            ]
        NSLayoutConstraint.activate(constraints)
    }
}
```

### Other tidbits:
```swift
// might be useful for scrolling to items:
// for macos: https://www.fpposchmann.de/animate-nsviews-scrolltovisible/
(void)accessibilityElementDidBecomeFocused
{
	[self.scrollView scrollRectToVisible:CGRectMake(self.frame.origin.x, self.frame.origin.y, self.bounds.size.width, self.bounds.size.height) animated:YES];
}

```
