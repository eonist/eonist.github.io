Some RegEx examples<!--more--> 

```swift
let str = "Serial: #YF 1942-b 12/01/17 (Scanned) 12/03/17 (Arrived) ref: 99/99/99"
let usDatePattern:String = "(\\d\\d)[-\\/](\\d\\d)[-\\/](\\d\\d(?:\\d\\d)?)"//Must be in the format: 12-30-1968 (mm-dd-yyyy) works with: "12-30-1968" and "12/30/1968" syntax
let matches = str.matches(usDatePattern)//RegExpMatch.datePattern
matches.forEach {
    let month:String = $0.value(str, 1)
    let day:String = $0.value(str, 2)
    let year:String = $0.value(str, 3)
    let dateStr:String = year + "/" + month + "/" + day
    let dateFormatter = DateFormatter()
    dateFormatter.dateFormat = "yy/mm/dd"
    let date:Date? = dateFormatter.date(from: dateStr)
    if date != nil {Swift.print(dateStr)}//Output: 17/12/01, 17/12/03
}
```