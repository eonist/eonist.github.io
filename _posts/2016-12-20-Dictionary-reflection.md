Reflection and UnWrapping now has Dictionary support <!--more--> 

### Swift example:

```swift
let temp:Temp = Temp([0:"test",3:"testing",5:"more testing"])//create a dict
let xml:XML = Reflection.toXML(temp) //reflect the dict to xml
Swift.print(xml.XMLString)//print the xml

let newInstance:Temp = Temp.unWrap(xml)!//unwrap the xml to dict
newInstance.someDict.forEach{
    Swift.print("key: \($0.0) value: \($0.1)")//print key,value
}

class Temp{
    var someDict:[Int:String]
    init(_ someDict:[Int:String]){
        self.someDict = someDict
    }
}
extension Temp:UnWrappable{
    static func unWrap<T>(xml:XML) -> T? {
        let someDict:[Int:String] = unWrap(xml,"someDict")
        return Temp(someDict) as? T
    }
}
```

### XML output:

```xml
<Temp>
	<someDict type="Dictionary">
		<item>
			<key type="Int">5</key>
			<value type="String">more testing</value>
		</item>
		<item>
			<key type="Int">0</key>
			<value type="String">test</value>
		</item>
		<item>
			<key type="Int">3</key>
			<value type="String">testing</value>
		</item>
	</someDict>
</Temp>
```

### Array with Dictionary reflection:

```swift
let temp:Temp = Temp()
let xml:XML = Reflection.toXML(temp)
Swift.print("xml.xmlString: " + "\(xml.xmlString)")
class Temp{
    let temp:[[String:String]] = [["a":"b"],["1":"2"]]
}
```

```xml
<Temp>
	<temp type="Array">
		<item type="Dictionary">
			<item>
				<key type="String">a</key>
				<value type="String">b</value>
			</item>
		</item>
		<item type="Dictionary">
			<item>
				<key type="String">1</key>
				<value type="String">2</value>
			</item>
		</item>
	</temp>
</Temp>

```

### Note:
Storing key and value in separate nodes is important because key can have different types and value can have nested nodes. Aka complex content. As can key when I think about it, as long as it extends the Hashable protocol. 