My notes on measuring cpu performance in swift code<!--more-->

### Measuring how long a function takes to finish

**Great for quick tests, or tests with callbacks**
```swift
let readTime: Date = .init()
sleep(2.2) // simulating doing heavy task
abs(readTime.timeIntervalSinceNow) // 2.2000
```

**Great for testing how long a closure takes to finish**
[https://github.com/eonist/TimeMeasure](https://github.com/eonist/TimeMeasure)⏱
```swift
print(TimeMeasure.timeElapsed { sleep(2.2) }) // 2.20000
print(TimeMeasure.timeElapsed { sleep(2.2); return 0 }.time) // 2.20000
print(TimeMeasure.timeElapsed { sleep(2.2); return 0 }.value) // 0 (retrieves the value)
let (val, time): (String, Double) = TimeMeasure.timeElapsed {
	sleep(2.2); return "abc"
}
print(val) // abc
print(time) // 2.20000
```
