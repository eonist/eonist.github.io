My notes on respobsive layout in swiftui <!--more-->

### ⭐ Layout protocol (AutoLayout via stack in swiftUI):
Ref: https://sarunw.com/posts/swiftui-custom-layout/

The first method we need to implement is sizeThatFits(proposal:subviews:cache:). We need to calculate and report how large the layout container is.

Available space that the container is given. We get this from the proposal parameter.
The size of the container's subviews. We can access this information through the subview proxy getting from the subviews parameter.

```swift
struct BackslashStack: Layout { // A custom Stack
    func sizeThatFits(proposal: ProposedViewSize, subviews: Subviews, cache: inout ()) -> CGSize {
        // Calculate and return the size of the layout container.
         print(proposal.width)
        // 100
        print(proposal.height)
        // 100
        let subviewSizes = subviews.map { proxy in
            return proxy.sizeThatFits(.unspecified)
        }
        // 2
        let combinedSize = subviewSizes.reduce(.zero) { currentSize, subviewSize in
            return CGSize(
                width: currentSize.width + subviewSize.width,
                height: currentSize.height + subviewSize.height)
        }
        
        // 3
        return combinedSize
    }
    
    func placeSubviews(in bounds: CGRect, proposal: ProposedViewSize, subviews: Subviews, cache: inout ()) {
        // Tell each subview where to appear.
         // 1
        let subviewSizes = subviews.map { proxy in
            return proxy.sizeThatFits(.unspecified)
        }
        
        // 2
        var x = bounds.minX
        var y = bounds.minY
        
        // 3
        for index in subviews.indices {
            let subviewSize = subviewSizes[index]
            let sizeProposal = ProposedViewSize(
                width: subviewSize.width,
                height: subviewSize.height)
            
            // 4
            subviews[index]
                .place(
                    at: CGPoint(x: x, y: y),
                    anchor: .topLeading,
                    proposal: sizeProposal
                )

            // 5
            x += subviewSize.width
            y += subviewSize.height
        }
    }
}
```

We have three ways to ask for subview size.

- The zero proposal; respond with the layout's minimum size.
- The infinity proposal; respond with the layout's maximum size.
- The unspecified proposal; respond with the layout's ideal size.

### Gotchas:
- Spacers with min max can be used to pin layout: https://www.hackingwithswift.com/quick-start/swiftui/how-to-make-a-fixed-size-spacer

### Resources:
- contional layout: https://swiftwithmajid.com/2022/08/16/conditional-layouts-in-swiftui/
- https://www.hackingwithswift.com/quick-start/swiftui/how-to-automatically-switch-between-hstack-and-vstack-based-on-size-class
- ⭐ ViewThatFits (A responsive problem solver view) https://sarunw.com/posts/swiftui-viewthatfits/
- https://sarunw.com/posts/swiftui-anylayout/
- https://www.hackingwithswift.com/quick-start/swiftui/how-to-dynamically-change-between-vstack-and-hstack
- layout priority: https://www.hackingwithswift.com/quick-start/swiftui/how-to-control-layout-priority-using-layoutpriority
- https://www.hackingwithswift.com/quick-start/swiftui/how-to-create-an-adaptive-layout-with-viewthatfits
- https://www.hackingwithswift.com/quick-start/swiftui/how-to-create-a-two-column-or-three-column-layout-with-navigationsplitview