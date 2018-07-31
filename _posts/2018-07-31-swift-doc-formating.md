My notes on method commenting in swift<!--more-->. IMO commenting is vital when creating complex systems. So much resolution about how the method came to be and it's artifacts and gotchas are lost if there is no documentation in the code. I usually include massive amounts of comments in the code because when I later revisit the code a year later, my initial thinking has disappeared and I need something to jog my memory to get back into the code. I also leave references to where parts of the solution was found, like stackoverflow links or other github projects. To give credit where credit is due. Personally I use a commenting style that is closer to javadoc as a lot of people know it and it feels universal. It even renders to apples dialog boxes like bellow:

```swift
 /**
  * A method that returns awesome colors
  * - PARAMETERS:
  *   - color: only awesome colors allowed
  *   - alpha: only awesome alpha values allowed
  */
 static func test(color:UIColor,alpha:CGFloat){
     _ = color
     _ = alpha
 }

```

Hold in the `alt` key and press the method name from a caller. And this dialog will pop up

<img width="430" alt="img" src="https://rawgit.com/stylekit/img/master/Screen Shot 2018-07-31 at 11.07.30.png">