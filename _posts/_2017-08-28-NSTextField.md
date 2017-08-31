

What is TextStorage?

### Resources:
- Hyper links in NSTextField: https://developer.apple.com/library/content/qa/qa1487/_index.html


This method in NSWindowDelegate can be used to figure out which textfield is in focus. windowWillReturnFieldEditor:toObject:


- Clickable URL in NSTextField: https://github.com/aptonic/frenzy/blob/master/DSClickableURLTextField.m

- Using Caret in NSTextField when parent frame is zero is very dificult: //make sure you are within frame, because fieldEditor cant work if not. It can but then you have to implement caret your self seems like the most sensible option. Supporting UI outside parent size isnt that important anyway! 👈