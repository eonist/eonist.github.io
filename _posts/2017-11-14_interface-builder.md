My notes on Interface Builder for iOS <!--more-->

### Vector assets
- You can use vector graphics but you have to use .pdf since ios has no support for xml or svg. You add .pdf assets by draging them into the assets.casette
- Supposedly you can add an pdf to a left bar nav button. But scaling it becomes a problem. A trick is to embedd the button in a UIView and add that to the left bar nav section. But then the button becomes unclickable. More research needed.

### Advance UI/UX animation:
every dribbble like UI style: takes a long time to code. and feels fragile. He is also an expert in IB and he struggles all the way through.
https://www.youtube.com/watch?v=MsNzYMMBIJ8&list=PLHDMmeIMXj8VRKB1w6L3igjFyhKi2hy9X&index=7

### Beginer IB project by apple:
https://developer.apple.com/library/content/referencelibrary/GettingStarted/DevelopiOSAppsSwift/BuildABasicUI.html

### Segue
Segue is essentially a way to get and set data from prev and next view . you can call `performSegue(withIdentifier:"nameOFSegueInIb",sender:self)` to transition to another view from swift code

## UIAlertController (alert dialog)
See 114 Spinners  Alerts.mp4 in complete-ios-10-developer-course

## Conclusion:
UIBuilder is rather simple. It's not much harder than using Applescript GUI. It consists of 3 parts. UI elements, Segues (aka transitions), outlets (refs in your code to the UI element) everything else is pretty trivial and solutions are a google search away.


### Let’s look at some facts about using Interface Builder:
- Interface Builder has a low entry barrier.
- Changes are visual.
- To get a full picture of a UI component, you need to inspect both an Interface Builder file and a source code file. Having two sources of truth for a UI component decreases readability and leaves more room for mistakes.
- Too many ways of configuring a component via IB: Runtime Attributes, multiple tabs with different settings, size-class-specific overrides, etc. This makes it difficult to spot which settings are overridden.
- Resolving merge conflicts in storyboards and xibs is error-prone since we have to deal with poorly readable XML. What is more, Interface Builder files can break silently while fixing merge conflicts. Say, you won’t notice a missing IBAction connection until you trigger that action.
- In complex scenarios, when you need more control over the layout (e.g., screen rotation, variations per screen size or platform, animations, dynamic fonts), you’ll need to resort to programmatic constraints or even manual frame calculation.

### Then let’s put some facts about programmatic layout:
- Code is a single source of truth of a UI component.
- View setup is explicit. It is clear which properties are configured and how.
- Merge conflicts are easier to resolve since we are dealing with code rather than XML.
- Extensible for complex scenarios: screen rotation, animations, dynamic fonts, etc.
- The consistent coding style of constructing UI programmatically is maintained across a mixed UIKit and SwiftUI project. With the gradual adoption of SwiftUI, this becomes more and more relevant.
- Less noise in the codebase: no segues, string identifiers, tricky initialization from storyboard and xib, numerous helper extensions attempting to hide some part of that noise.

### Conclusion:
By looking at these facts we can conclude that the folklore was wrong. Layout in code is faster and more future-proof compared to storyboards and xibs. It is seemingly fast and easy to throw some views together on a canvas. However, in the long run, all of the disadvantages of Interface Builder add up, making it a sub-optimal choice.
