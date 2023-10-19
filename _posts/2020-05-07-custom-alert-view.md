Notes on custom UIAlertController<!--more-->

### Resources:
- [https://github.com/podkovyrin/DWAlertController](https://github.com/podkovyrin/DWAlertController)

### Code
- Custom alert views:
[https://github.com/DrankoLQ/CustomAlertView](https://github.com/DrankoLQ/CustomAlertView)
And   
[https://medium.com/if-let-swift-programming/design-and-code-your-own-uialertview-ec3d8c000f0a](https://medium.com/if-let-swift-programming/design-and-code-your-own-uialertview-ec3d8c000f0a)

```swift
/**
 * Add
 * - Note: ref: https://stackoverflow.com/a/38621779
 * - Note: remove overlay: overlay.removeFromSuperview()
 */
@discardableResult func addBlurOverlay() -> UIVisualEffectView {
	let blurEffect = UIBlurEffect(style: .systemUltraThinMaterial)
	let blurView = UIVisualEffectView(effect: blurEffect)
	blurView.frame = UIScreen.main.bounds // self.bounds
	//blurEffectView.autoresizingMask = [.FlexibleWidth, .FlexibleHeight] // for supporting device rotation
	view.addSubview(blurView)
	return blurView
}
```

### Links:
- Ref: https://stackoverflow.com/a/47925120/5389500
- Related: https://github.com/pmusolino/PMAlertController
- Related: https://github.com/vikmeup/SCLAlertView-Swift
- Related: https://williamboles.me/finding-hope-with-custom-alerts/
- https://github.com/pmusolino/PMAlertController  
- https://github.com/vikmeup/SCLAlertView-Swift  
- https://williamboles.me/finding-hope-with-custom-alerts/ 
- For macOS and iOS: https://github.com/sentryco/UpgradeAlert