My notes on Compression, resistance, priority when using AutoLayout<!--more-->

## Overview:

- Hugging: content does not want to grow
- Compression Resistance: content does not want to shrink

## Related:
- In conjunction with: `.lessThanOrEqaul`, `.greaterThanOrEqual`, `.equal`
- constraint.priority

### Intrinsic Content Size
- Pretty self-explanatory, but views with variable content are aware of how big their content is and describe their content's size through this property. Some obvious examples of views that have intrinsic content sizes are UIImageViews, UILabels, UIButtons.

### Content Hugging Priority
- The higher this priority is, the more a view resists growing larger than its intrinsic content size.
- Content Compression Resistance Priority - The higher this priority is, the more a view resists shrinking smaller than its intrinsic content size.

### Resources:
- [https://developer.apple.com/documentation/uikit/uiview/1622485-setcontenthuggingpriority](https://developer.apple.com/documentation/uikit/uiview/1622485-setcontenthuggingpriority)  
