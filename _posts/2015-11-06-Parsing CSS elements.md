When a CSS string has resolved all its links and is free of comments parsing it into Style objects becomes less difficult. The basic setup is as follows (uses a couple of RegEx matches with capturing groups to extract the values)<!--more--> :  
   
```swift   
for style in CSS{
   for styleElement in style{
      for property in styleElement{
         let propertyName
         let propertyValue
      }
   }
}
```