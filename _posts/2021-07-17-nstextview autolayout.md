My notes on Autolayout and NSTextView / UITextview <!--more-->

⚠️️ This article is still WIP ⚠️️ 

The key to make text view height follow it's content is by `NOT SET HEIGHT CONSTRAINT` and `DISABLE THE SCROLL`.

```swift
theTextView.isScrollEnabled = false
theTextView.text = "some text"
theTextView.sizeToFit()
```
But, if you already set the height constraint, then make it inactive

```swift
theTextViewHeightConstraint.isActive = false

theTextView.isScrollEnabled = false
theTextView.text = "some text"
theTextView.sizeToFit()
```


### Set height constraint to self.contentSize.height
```swift
self.invalidateIntrinsicContentSize()
```

Maybe
```swift
TextViewHeightConstraint.constant = [TextView intrinsicContentSize].height
[self.myText sizeToFit];
```

```swift
func textViewDidChange(textView: UITextView) {

    // dynamic height adjustments

    var height = ceil(textView.contentSize.height) // ceil to avoid decimal

    if height != textViewHeight.constant { // set when height changed
        textViewHeight.constant = height
        textView.setContentOffset(CGPointZero, animated: false) // scroll to top to avoid "wrong contentOffset" artefact when line count changes
    }
}

```

or

All you have to do is:

set up all your constraints except the height one AND
set textView's scrollEnabled property to NO
The last part is what does the trick.

Your text view will size automatically depending on it's text value.



`myTextView.textContainer.heightTracksTextView = true`
Which allows scroll to be enabled


it is possible. create textview of one line. attach leading,trailing,bottom to its superview and give height constraint of greater than equal to 40. this will give minimum height constraint to textivew and as and when you start typing, you need to keep checking if content goes onto second line and increase height by changing constant value of height constraint.



Here's a subclass of UITextView I've come up with that allows auto expansion with auto layout but that you could still constrain to a maximum height and which will manage whether the view is scrollable depending on the height. By default the view will expand indefinitely if you have your constraints setup that way.
https://stackoverflow.com/a/41770390/5389500

```swift
import UIKit

class FlexibleTextView: UITextView {
    // limit the height of expansion per intrinsicContentSize
    var maxHeight: CGFloat = 0.0
    private let placeholderTextView: UITextView = {
        let tv = UITextView()

        tv.translatesAutoresizingMaskIntoConstraints = false
        tv.backgroundColor = .clear
        tv.isScrollEnabled = false
        tv.textColor = .disabledTextColor
        tv.isUserInteractionEnabled = false
        return tv
    }()
    var placeholder: String? {
        get {
            return placeholderTextView.text
        }
        set {
            placeholderTextView.text = newValue
        }
    }

    override init(frame: CGRect, textContainer: NSTextContainer?) {
        super.init(frame: frame, textContainer: textContainer)
        isScrollEnabled = false
        autoresizingMask = [.flexibleWidth, .flexibleHeight]
        NotificationCenter.default.addObserver(self, selector: #selector(UITextInputDelegate.textDidChange(_:)), name: Notification.Name.UITextViewTextDidChange, object: self)
        placeholderTextView.font = font
        addSubview(placeholderTextView)

        NSLayoutConstraint.activate([
            placeholderTextView.leadingAnchor.constraint(equalTo: leadingAnchor),
            placeholderTextView.trailingAnchor.constraint(equalTo: trailingAnchor),
            placeholderTextView.topAnchor.constraint(equalTo: topAnchor),
            placeholderTextView.bottomAnchor.constraint(equalTo: bottomAnchor),
        ])
    }

    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

    override var text: String! {
        didSet {
            invalidateIntrinsicContentSize()
            placeholderTextView.isHidden = !text.isEmpty
        }
    }

    override var font: UIFont? {
        didSet {
            placeholderTextView.font = font
            invalidateIntrinsicContentSize()
        }
    }

    override var contentInset: UIEdgeInsets {
        didSet {
            placeholderTextView.contentInset = contentInset
        }
    }

    override var intrinsicContentSize: CGSize {
        var size = super.intrinsicContentSize

        if size.height == UIViewNoIntrinsicMetric {
            // force layout
            layoutManager.glyphRange(for: textContainer)
            size.height = layoutManager.usedRect(for: textContainer).height + textContainerInset.top + textContainerInset.bottom
        }

        if maxHeight > 0.0 && size.height > maxHeight {
            size.height = maxHeight

            if !isScrollEnabled {
                isScrollEnabled = true
            }
        } else if isScrollEnabled {
            isScrollEnabled = false
        }

        return size
    }

    @objc private func textDidChange(_ note: Notification) {
        // needed incase isScrollEnabled is set to true which stops automatically calling invalidateIntrinsicContentSize()
        invalidateIntrinsicContentSize()
        placeholderTextView.isHidden = !text.isEmpty
    }
}
```

I needed a text view that would automatically grow up until a certain maximum height, then become scrollable. Michael Link's answer worked great but I wanted to see if I could come up with something a bit simpler. Here's what I came up with:


```swift
class AutoExpandingTextView: UITextView {

    private var heightConstraint: NSLayoutConstraint!

    var maxHeight: CGFloat = 100 {
        didSet {
            heightConstraint?.constant = maxHeight
        }
    }

    private var observer: NSObjectProtocol?

    override init(frame: CGRect, textContainer: NSTextContainer?) {
        super.init(frame: frame, textContainer: textContainer)
        commonInit()
    }

    required init?(coder: NSCoder) {
        super.init(coder: coder)
        commonInit()
    }

    private func commonInit() {
        heightConstraint = heightAnchor.constraint(equalToConstant: maxHeight)

        observer = NotificationCenter.default.addObserver(forName: UITextView.textDidChangeNotification, object: nil, queue: .main) { [weak self] _ in
            guard let self = self else { return }
            self.heightConstraint.isActive = self.contentSize.height > self.maxHeight
            self.isScrollEnabled = self.contentSize.height > self.maxHeight
            self.invalidateIntrinsicContentSize()
        }
    }
}
```

Do not give a static height to UIView or to UITextView

```swift
func textviewHeight() {

        let contentSize = self.TextView.sizeThatFits(self.TextView.bounds.size)
        var frame = self.TextView.frame
        frame.size.height = contentSize.height
        self.TextView.frame = frame

        self.aspectRatioTextView = NSLayoutConstraint(item: self.TextView, attribute: .Height, relatedBy: .Equal, toItem: self.TextView, attribute: .Width, multiplier: TextView.bounds.height/TextView.bounds.width, constant: 1)
        self.TextView.addConstraint(self.aspectRatioTextView!)

        self.mainViewHeightConstraint.constant = self.mainViewHeightConstraint.constant + self.TextView.frame.height
    }
```


### Dynamic Sizing of UITextView with Autolayout

I had been working on a project where the goal was to arrange a UITextView and some UILabel/UITextFields such that all of the elements were embedded in a UIScrollView and responded to keyboard events. The UITextView would have varying lengths of text and would need to display it without the need to scroll.
And sure, there are some solutions that make use of sizeThatFits and setFrame , but I found one other way to do this that seems cleaner and leverages a UITextView’s underlying components (NSLayoutManager, NSTextContainer, NSTextStorage)and Autolayout.
Implementation
After ensuring that the textView.text has been set, you can do the following:


Explanation:
I have the constraints defined for my UITextView in my storyboard; they are simply pinning the top, leading, and trailing edges to its UIScrollView super view, while also defining a low-priority bottom and height constraint. These two constraints (bottom & height) are both set in storyboard solely to silence warnings about ambiguous constraints.
Line 2 stores the UIEdgeInsets of the UITextView, which by default are top: 8, left: 0, bottom: 8, right: 0. These values are needed to factor into the overall height.
Line 3 accesses the NSLayoutManager of the UITextView in order to call usedRect(for:) which “returns the text container’s currently used area, which determines the size that the view would need to be in order to display all the glyphs that are currently laid out in the container.”
usedRect(for:) is passed the textView.textContainer (NSTextContainer) property.
Presumably, usedRect(for:) calls on textView.textContainer.size in order to determine its bounds.
The animation on lines 9–12 were added to make the resizing feel smoother, but can be considered completely optional.
And there you have it, a simple 3-line solution to dynamic UITextView height using its underlying CoreText-based components.

```swift
func updateTextViewHeight(animated: Bool) {
   let textContainterInsets = self.textView.textContainerInset
   let usedRect = self.textView.layoutManager.usedRect(for: self.textView.textContainer)

   // This is a reference to the constraint defined in storyboard
   self.textViewHeightConstraint.constant = usedRect.size.height + textContainterInsets.top + textContainterInsets.bottom

   // animate if desired
   if !animated { return }
   UIView.animate(withDuration: 0.2, animations: {
     self.view.layoutIfNeeded()
   })
 }
```

### If you prefer to do it all by auto layout:

In Size Inspector:

Set Content Compression Resistance Priority Vertical to 1000.

Lower the priority of constraint height for your UITextView. Just make it less than 1000.
Uncheck Scrolling Enabled.


### Maybe solution:

```swift
public static nfloat GetEstimateHeight(this UITextView textView, UIView View)
{
    var size = new CoreGraphics.CGSize(View.Frame.Width, height: float.PositiveInfinity);
    var estimatedSize = textView.SizeThatFits(size);
    return estimatedSize.Height;
}
var size = new CoreGraphics.CGSize(View.Frame.Width, height: float.PositiveInfinity);
    var estimatedSize = textView.SizeThatFits(size);
    var textViewFinalHeight = estimatedSize.Height;
```


### Size to fit:

```swift
theTextViewHeightConstraint.isActive = false

theTextView.isScrollEnabled = false
theTextView.text = "some text"
theTextView.sizeToFit()
```

### Resources:
- dealing with keyboard coming into play: https://pinkstone.co.uk/how-to-resize-a-uitextview-dynamically-with-auto-layout/
- https://lapcatsoftware.com/articles/autoresizing-uitextview.html


### Todo: 
- write about intrinsicContentSize
- and fittingsize
- and .contentSize.height
- and $0.sizeToFit()
- and let textWidth: CGFloat = text.size(withAttributes: [.font: Font.title]).width + 12 // calcs width here
