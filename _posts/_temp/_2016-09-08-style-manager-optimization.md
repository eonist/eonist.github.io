Figure out how to store css in style-manager with hash

Every element runs over every style added to the styleManager

So by hashing each style 

Also create a cache system where you store the last 20 queries: 

You would need to store each style selector in a separate hash id.

And then instead of looping over style.selectors you loop over elementQuerrySelectors and for each selector found you querry the hash id. This could potentially increase look-up-time because so many more lookups would happen. Maybe the cache is good enough

You could create a hash table based on the num of selectors in a style. Because if a style has more selctors than the element -> then its not within scope and will not be used. This way you can decrease lookup-time. 

And also the last element.selector must match the last element.selectors so we could create a hash table. The exception is the values that can inherit. (we could make a different system for these values)

A seperate array for styles that have inheritable values. and filtering values upon match.



**element selectors: **

```
Window Button#custom Text#buttonText
```

**Style Selector:**

```
Button Text (applies)
Window TextArea (does not apply)
Window (partly applies, font etc)
```


 
```swift
//cache idea (sudo code):
cache.recentlyUsed = []

//cache.style()

//you ask StyleResolver.style for the style. which has
//cache then queries the 10 last used styles it has in cache.styles
	//if cache.styles doesn't have the absolute style. 
		//style = StyleResolver.resolveStyle()
		//let idx = -1
		//for i < .count{
			//if($0.address == absolute-style-address){
				//idx = i
				//break
		//if(idx != -1)//already exists in cache
			ArrayModifier.indexSwap(.recentlyUsed,idx)//move to front
		//else//does not exist in cache
			//if(recentlyUsed.count > 9){
				//recently.popLast()
			//recently.unshift((style,address))//add to front
				

//let absolute-element-selector-address = ElementParser.stackString(button) 
//let address = SelectorParser.string($0.selectors)

```
The cache system should also move the most popular queries to the top of the array list
so basically the the cache styles should be like this -> cache.styles:[(IStyle,Int)] (<--using duplets where int is the popularity count) (popularity is good for repeating calls to styleResolver. like on hover etc)

```swift
StyleManger.getStyles(elementSelectors.count)

var maxSelectorCount:Int?
var minSelectorCount:Int?
var hashedStyles:Dictionary<selectorCount,[IStyle]>
//[]?
/*
 * NOTE: the hashedStyles are sorted on selectorCount from 1 and upwards
 */
func getStyles(selectorCount:Int)-> [IStyle]{
    var styles:[IStyle]
    for i = selectorCount; count < maxSelectorCount; i++;{
        if let value = hashedStyles[i.string]{
            styles += value
        }
    }
    return styles
}
```

```swift
//Grouping code:
/**
 * Creates a directory with lists of styles. if style.selector.count is 3, then its added to the key:3 and also every key that is bigger than 3
 * PARAM: f: the key to group by (its a method that gets each item in items)
 * PARAM: items: the items to be grouped
 * PARAM: o: is an item in items
 * 
 */
func groupby<T, H: Hashable>(items: [T], f: (T) -> H) -> [H: [T]] {
    return items.reduce([:], combine: { (var ac: [H: [T]], o: T) -> [H: [T]] in
        let selectorCount:Int = f(o)/*h is the key, an item is passed to f to get h*/
        if var c = ac[selectorCount] {/*if something already exist at key: h then append to that value*/
            c.append(o)
            ac.updateValue(c, forKey: selectorCount)/*re-add that value*/
        }
        ac.keys.forEach{
             if(h > $0) {
                ac.updateValue([o], forKey: selectorCount)/*add the item from items as an array*/
            }
        }
        return ac/*Return the grouped list*/
    })
}
let result = groupby([Style(),Style(),Style()], f: { $0.selectors.count })
```

- [x] build the recently queries cache system (its easy) 
- [ ] build the selector-count hashtable system (its easy too)
- [ ] build the cache system that is based on popularity
- [x] Test if speed goes up after implementing these two optimization efforts (use a timer before and after)
- [ ] More struct classes will speed things up probably. Style could be struct maybe? 
- [ ] Use Enum instead of constants?
- [ ] consider to build selector.last hashtable that would speed things up even further
- [ ] Figure out why copying values is faster than changing them. 

 //test how many element selector addresses are similar
        
        //then do the last selector optimization that doesnt work with inheritage. 
        
        //find which variable inherite, can these be added to its own array, so that one array is at least optimized.
        
        
```swift
//new idea:
//a selector has: element,id,state,class

//getStyles[element,id,state,class]->[IStyle]

//Window#customWin TextButton Text#title//<--4 selectors
//

//getStyles(selector)//<--basically the last Selector entity so Button#custom for instance is an selector entity

func getStyles(selector:Selector)->IStyle{
    var styles:[IStyle] = []
    styles += stylesByElement[selector.element]
    styles += stylesByID[selector.id]
    styles += stylesByClassId[selector.classId]
    styles += stylesByState[selector.state]
}
func hashStyle(style:IStyle){
    //when you add styles:
    if(style.selectors.lastItem.element != ""){
        if let styles = stylesByElement[style.selectors.lastItem.element]{
            styles.append(style)
        }else{
            stylesByElement[style.selectors.lastItem.element] = [style]
        }
    }
    if(style.selectors.lastItem.id != ""){
        
    }
    if(style.selectors.lastItem.classIds.count > 0){
        
    }
    if(style.selectors.lastItem.states.count > 0){
        
    }    
}

//Window
    //TextButton
        //Text
//TextButton
//Text
//#customWin

```

//shaving off 1 sec of 6 secs, isnt going to cut it. research how other css engines work so well.
//make style into struct
//research why struct is faster or slower

```swift
//Another idea would be to flatten the styles when making them:
//try the idea with simple classes. A style with only Element for instance
//write tree structures down on post-its -> see if you can figure something out



//Window#Custom Button#special:over Text
    //fill:red; 
//Button Text
    //fill:pink;
//Text
    //fill:blue;
    //line:green;//<--insert line:Green in the above style. 
    
    
//existing styles derive properties from every new style that are added.
//and finally the added style try to derive from every existing style

//can we store the styles in tree structures?
//Window#custom List Button#special:over Text#title
    
//Branch.element == Window//true
    //branch.element == List //false
    //else
        //branch.element == button //true
            //branch.text == Text
                //use these style properties
                
//tree structure is also faster to traverse when appending a new style and expanding the style at the same time. IT can derive and apply properties as it traverses the tree, and probably fast, because it can disregard a lot of avenues


```

```swift
protocol ISelector{
    var element:String{get set}
    var id:String{get set}
    
}
class Selector:ISelector{
    var element:String
    var id:String
    init(element:String,_ id:String){
        self.selement = element
        self.id = id
    }
}
protocol IStyle{
    var selectors:[ISelector]{get set}
    var properties:[(name:String,value:Any)]
}
class Style:IStyle{
    var selectors:[ISelector]
    var properties:[(String,Any)]
    init(selectors:[ISelector],properties:[(String,Any)]){
        self.selectors = selectors
        self.properties = properties
    }
}

let a:IStyle = Style([Selector("Text")],[("fill","blue"),("line","green")])
let b:IStyle = Style([Selector("Window","custom"),Selector("Button","special"),Selector("Text")],[("fill","red")])


```

//The fastest way is of course to comprise the styles and then save them in an xml File for the next run. and then just reload all the styles with direct addressing, then if there is a change in the css, re-comprise the styles again.
//you also need to continue to save absolute addresses for styles. onHover etc