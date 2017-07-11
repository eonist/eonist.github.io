When you want to assert if a digit is a digit i used to use a simple conditional back referencing pattern. <!--more--> As this is not possible in swift i did the next best thing:

```swift
let testCases:Array<String> = ["2.3","2","44","22.11","-2.3","-2","-44","-22.11","20.",".10",".3","1.",".","-20.","-.10","-.3","-1.","-."]
        
let g1:String = "(?=\\s|^)"/*must be preceeded by a space char or the begining*/
let g2:String = "\\-*\\d+?"/*optional minus sign followed by one or more digits*/
let g3:String = "(?=\\.\\d|\\s|,|$)"/*Must be followed by a dot with a digit or space, comma or end*/
let g4:String = "((?<=\\d)\\.(?=\\d))*"/*zero or one dot char that must be preceeded with a digit and subseeded wutha digit*/
let g5:String = "((?<=\\d\\.)\\d+?)*"/*zero or more digits that must be preceeded with a digit and a char*/
let g6:String = "(?=$)"/*followed by an end or */

let pattern:String = g1 + g2 + g3 + g4 + g5 + g6
for testCase in testCases{
    Swift.print(testCase.test(pattern))
}
```

==With conditional backreferencing you could have written this as simple as==: 

```swift
let pattern:String = "\\-?\\d*?(\\.?)(?(\\1)\\d+?)"//where the ?(\\1) asks if a dot was found or not
```

- [ ] Check the RegExp books, there may be a ref to objc RegEx about this subject in one of them. 
- [ ] read the reg ex specs on apple.com again, maybe you missed a reg ex class init flag or alike to support conditionals
