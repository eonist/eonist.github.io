A Selector is a data container that contains element, classIds, ids and states. These values are derived from a Style name<!--more--> 
```swift
"Text#sectionText:down{fill:blue}"
//Element:Text
//id:sectionText
//state:down
```

## **Sibling styles**
CSS code like this:  
```swift
"Section[Button,CheckButton]{fill:red;}"
//Becomes 2 styles: "Section Button" and "Section CheckButton"
```


You can even do this:
```swift
"Section[RadioButton,CheckButton]Icon{fill:red;}"
//Becomes 2 styles: "Section RadioButton Icon" and "Section CheckButton Icon"
```


You can also do this:
```swift
"[RadioButton,CheckButton]Icon{fill:red;}"
//Becomes 2 styles: "RadioButton Icon" and "CheckButton Icon"
```