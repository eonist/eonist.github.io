Notes on nssplitview<!--more--> 

Maybe use split view for column layout?

https://stackoverflow.com/questions/44309088/nssplitviewcontroller-programmatically/46756790

https://github.com/twodayslate/SplitView
https://github.com/bibinjacobpulickal/BBDragResizableView
https://github.com/AdilSoomro/Resizable
https://github.com/rcholic/ResizableView


https://stackoverflow.com/questions/19360805/drag-to-resize-nsview-or-other-object


https://github.com/macteo/SplitKit (very messy, can have some interesting parts)
https://github.com/macteo/SabBar (also supper messy)


Very complex but relevant: https://developer.apple.com/documentation/appkit/cocoa_bindings/navigating_hierarchical_data_using_outline_and_split_views


Has a lot of useful info on NSSplitView:

https://betterprogramming.pub/using-nssplitview-in-an-macos-app-bbe2438bfe3e


```swift
import Foundation
/**
 * - Fixme: ⚠️️ lots of inspo: https://github.com/bibinjacobpulickal/BBDragResizableView/tree/master/AppKit%20Cocoa%20Resizable%20NSView
 */
//override func updateTrackingAreas() {
//   super.updateTrackingAreas()
//   trackingAreas.forEach { area in
//      removeTrackingArea(area)
//   }
//   addTrackingRect(bounds)
//}
//override func mouseExited(with event: NSEvent) {
//   super.mouseExited(with: event)
//   NSCursor.arrow.set()
//}
//override func mouseDown(with event: NSEvent) {
//   super.mouseDown(with: event)
//   let locationInView = convert(event.locationInWindow, from: nil)
//   draggedPoint            = locationInView
//}
//override func mouseUp(with event: NSEvent) {
//   super.mouseUp(with: event)
//   draggedPoint = .zero
//}
//override func mouseMoved(with event: NSEvent) {
//   super.mouseMoved(with: event)
//   let locationInView = convert(event.locationInWindow, from: nil)
//   cursorBorderPosition(locationInView)
//}
//override func mouseDragged(with event: NSEvent) {
//   super.mouseDragged(with: event)
//   borderWidth                     = resizableArea
//   let locationInView              = convert(event.locationInWindow, from: nil)
//   let horizontalDistanceDragged   = locationInView.x - draggedPoint.x
//   let verticalDistanceDragged     = locationInView.y - draggedPoint.y
//   let cursorPosition              = cursorBorderPosition(draggedPoint)
//   if cursorPosition != .none {
//      let drag    = CGPoint(x: horizontalDistanceDragged, y: verticalDistanceDragged)
//      if checkIfBorder(cursorPosition, exceedsSuperviewBorderWithPadding: 12, andDraggedOutward: drag) {
//         return
//      }
//   }
//   switch cursorPosition {
//   case .top:
//      size.height += verticalDistanceDragged
//      draggedPoint = locationInView
//   case .left:
//      origin.x    += horizontalDistanceDragged
//      size.width  -= horizontalDistanceDragged
//   case .bottom:
//      origin.y    += verticalDistanceDragged
//      size.height -= verticalDistanceDragged
//   case .right:
//      size.width  += horizontalDistanceDragged
//      draggedPoint = locationInView
//   case .none:
//      break
//   }
//}
//@discardableResult
//func cursorBorderPosition(_ locationInView: CGPoint) -> BorderPosition {
//   if locationInView.x < resizableArea {
//      NSCursor.resizeLeftRight.set()
//      return .left
//   } else if locationInView.x > bounds.width - resizableArea {
//      NSCursor.resizeLeftRight.set()
//      return .right
//   } else if locationInView.y < resizableArea {
//      NSCursor.resizeUpDown.set()
//      return .bottom
//   } else if locationInView.y > bounds.height - resizableArea {
//      NSCursor.resizeUpDown.set()
//      return .top
//   } else {
//      NSCursor.arrow.set()
//      return .none
//   }
//}
//
//private func checkIfBorder(_ border: BorderPosition,
//                           exceedsSuperviewBorderWithPadding padding: CGFloat,
//                           andDraggedOutward drag: CGPoint) -> Bool {
//   if border == .left && frame.minX <= padding && drag.x < 0 {
//      return true
//   }
//   if border == .bottom && frame.minY <= padding && drag.y < 0 {
//      return true
//   }
//   guard let superView = superview else { return false }
//   if border == .right && frame.maxX >= superView.frame.maxX - padding && drag.x > 0 {
//      return true
//   }
//   if border == .top && frame.maxY >= superView.frame.maxY - padding && drag.y > 0 {
//      return true
//   }
//   return false
//}
//
//enum BorderPosition {
//   case top, left, bottom, right, none
//}

```

If we want to use native nssplitview in the future. this has a great starter kit: https://stackoverflow.com/questions/44309088/nssplitviewcontroller-programmatically/46756790
