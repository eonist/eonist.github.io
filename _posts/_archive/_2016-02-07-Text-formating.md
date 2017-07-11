Overview of text formatting <!--more--> 

**InputText**  
<img width="213" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-02-08 at 20.35.48.png">

**TextArea**  
<img width="274" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2016-02-08 at 19.55.06.png">

**Multiline TextArea**
Coming soon


**NOTE:** 
I think NSView.hitView(aPoint:CGPoint):NSView flips the aPoint if it detects an NSText or NSTextField. Since if i manually do hitTesting on NSText or NSTextField with the aPoint form the parent hitTest call. It doesn't work. My idea forward is to subclass NSText and NSTextField and add this flipping there. rather than add an if clause in the base class Element

**NOTE:**
NSText doesn't like being subclassed. It asks you to implement every method it has or els it wont compile. And you shouldn't call super. To mitigate this you could use swivel (not going to do that, because its a hack, how does it work in playground for instance) or just make a wrapper that has it as a subview, or use NSTextField instead. (needs research, pro cons etc) Maybe use NSText for Text Elements that only has static or dynamic text. And then use NSTextField for Text that can be edited. aka Input text (Editable text). You could also implement every method in NSText and sneak in a super call only in the hittest. i think it still would be appstore approvable. As its not a private api call. You could also research how nsviews actually changes their hitTest point down the hierarchy. Maybe its offset. then you wouldn't need to implement hittest in the nsview it self. Seems like NSTextField is the way to go. As textview seems to be more appropriate for text editor apps etc ref: [here](https://developer.apple.com/library/mac/documentation/TextFonts/Conceptual/CocoaTextArchitecture/TextFieldsAndViews/TextFieldsAndViews.html#//apple_ref/doc/uid/TP40009459-CH8-BBCFEBHA) 

**Experiment:** ( to test how NSView sends the point down the hierarchy)
<img width="1024" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/My Ideas - 42323d2q.png">

## Observations:

1. NSTextField doesn't support the enter key for newlines (you probably have to use NSText for that) (there is a property named: lineBreakMode in the NSMutableParagraphStyle class)

## Tasks:
- [ ] do the NSView hitTest hierarchy test


## Notable findings:
- Strips text for newlines when using NSTextField: http://briksoftware.com/blog/?p=17

## NOTE:

To get text to be bold you must use the bold version of the font

## variable overview:  

```swift
TextField:

/**
* TextFiled values:
* alwaysShowSelection : Boolean false
* antiAliasType : String = flash.text.AntiAliasType.ADVANCED
* autoSize : String = TextFieldAutoSize.NONE / TextFieldAutoSize.LEFT / TextFieldAutoSize.RIGHT / TextFieldAutoSize.CENTER ,left,right,none,center
* background : Boolean = false
* backgroundColor : uint = 0xFFFFFF
* border : Boolean false
* borderColor : uint = 0x000000
* bottomScrollV : int (read only)
* caretIndex : int read only
* condenseWhite : Boolean = false
* defaultTextFormat : TextFormat ( new TextFormat())
* displayAsPassword : Boolean false
* embedFonts : Boolean false 
* gridFitType : String = flash.text.GridFitType.PIXEL
*   : String = “”
* length : int read only
* maxChars : int = 0
* maxScrollH : int read only
* maxScrollV : int read only
* mouseWheelEnabled : Boolean = true
* multiline : Boolean = false
* numLines : int = read only
* restrict : String = null (my_txt.restrict = "A-Z 0-9”;)(my_txt.restrict = "^a-z";) (see adobe doc for more restricts)
* scrollH : int ?
* scrollV : int ?
* selectable : Boolean = true
* selectionBeginIndex : int read
* selectionEndIndex : int read
* sharpness : Number = 0
* styleSheet : StyleSheet (new StyleSheet ())
* text : String = “”
* textColor : uint = (0x000000)
* textHeight : Number  = read only
* textWidth : Number = read only
* thickness : Number  = 0
* type : String = TextFieldType.STATIC / TextFieldType.DYNAMIC, TextFieldType.INPUT / dynamic, input, static
* useRichTextClipboard : Boolean = false
* wordWrap : Boolean = false
* 
* inherited (InteractiveObject):
* mouseEnabled = true
* width = 100;
* height = 100;
*/


TextFormat:
/**
 * Textformat default values:
 * align = "left"
 * blockIndent = 0
 * bold = false
 * bullet = false
 * color = 0x000000
 * font = "Times New Roman" (default font is Times on Mac OS X)
 * indent = 0
 * italic = false
 * kerning = false
 * leading = 0
 * leftMargin = 0
 * letterSpacing = 0
 * rightMargin = 0
 * size = 12
 * tabStops = [] (empty array)
 * target = "" (empty string)
 * underline = false
 * url = "" (empty string)
 */
 
```


More paragraphStyle goodies: 

https://www.objc.io/issues/9-strings/string-rendering/