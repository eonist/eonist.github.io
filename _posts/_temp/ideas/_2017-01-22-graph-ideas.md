


//try making DayGraph which has Day names (remember to return Graph as identifier)
//contemplate removing the top value and centering the graph a bit more. Basically the graph would stil be contained in a perfect frame.
//also use 5/3 ratio for a more widescreen look
//add a user-icon (google material kit icon) that works as a dropdown menu, where you can select users and compare commit linegraphs that then gets different colors (should also be able to filter commits & codeLine.additions & codeLine.deletions), also filter project via repo browser.



```swift

/**
 * the values are the collected values from 1 to end of month
 */

class YearGraph:Graph{
    override var hValNames:[String] {return ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"]}
    override var hValues:[CGFloat] {return [14,8,13,17,25,9,14,20,33,25,15,19]}
}
*/
/*
class WeekGraph:Graph{
    override var hValues:[CGFloat] {return [4,2,3,7,5,0,1]}
    override var hValNames:[String] {return ["M","T","W","T","F","S","S"]}
}
*/




class MonthGraph:Graph{
    override var hValues:[CGFloat] {
        var arr:[CGFloat] = []
        for _ in 0..<numOfDaysInMonth{
            let val:CGFloat = NumberParser.random(4, 24).cgFloat//generate hValues via random
            arr.append(val)
        }
        return arr
    }
    override var hValNames:[String] {
        var arr:[String] = []
        for i in 1...numOfDaysInMonth{//you need 1 until numOfDaysInMonth as hvalnames
            arr.append(i.string)
        }
        return arr
    }
    var numOfDaysInMonth:Int
    var curMonth:Int
    init(_ width: CGFloat, _ height: CGFloat,_ curMonth:Int, _ parent: IElement?, _ id: String? = nil) {
        self.curMonth = curMonth
        let date:NSDate = NSDate.createDate(nil,4)!
        numOfDaysInMonth = date.numOfDaysInMonth
        
        super.init(width, height, parent, id)
    }
    required init?(coder: NSCoder) {fatalError("init(coder:) has not been implemented")}
}

```
//day,week,month,year,all (focus on day and week)
//12a 1a 2a 3a 4a 5a 6a 7a 8a 9a 10a 11a 12p 1p 2p 3p 4p 5p 6p 7p 8p 9p 10p 11p
//00:00, 01:00, 02
//00,02,04,06,08,10,12,14,16,18,20,22,24 (12 part time)
//00:00,04:00,08:00,12:00,16:00,20:00 ()
//00,04,08,12,16,20 (6 part time)

//Night 00, Morning 06, Noon 12, evening: 18 night 00

//00,03,06,09,12,15,18,21,00
//12a,03a,06a,09a,12a,03p,06p,09p,12a