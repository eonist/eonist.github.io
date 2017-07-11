There is no support for Default values in a protocol method arguments when using swift.<!--more--> 

```
Error message: "Default argument not permitted in a protocol method"
```

**Workaround:**  

```swift
protocol IStyle {
    func getStyleProperty(name:String,_ depth:Int)->IStyleProperty?
}

extension IStyle{
    /**
     * Convenience method since apple doesnt support default values in protocols
     * NOTE: Default argument not permitted in a protocol method
     */
    func getStyleProperty(name:String)->IStyleProperty?{
        return getStyleProperty(name, 0)
    }
}
```


Thats it. Now you have default values in swift protocols. (Do some tests in playground to verify if this works in all cases)