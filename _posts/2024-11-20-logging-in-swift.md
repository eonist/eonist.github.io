In Swift, there are multiple ways to log messages to the console for debugging purposes. Here are the main methods<!--more-->

### 1. Using print
The simplest way to log messages to the xcode-console:

```swift
print("This is a log message") // This is a log message
```

### 2. Using NSLog
NSLog is a lower-level API that automatically includes a timestamp in the log. It’s useful for debugging:

```swift
NSLog("This is a log message with timestamp") // 2024-11-06 10:00:00.000 AppName[12345:67890] This is a log message with timestamp
```

### 3. Using os_log (Preferred for Production)
The os_log API is part of the os framework and is ideal for structured logging. It's more efficient and allows you to categorize and filter logs in Console.app.

Import the os Framework:

```swift
import os

let logger = Logger(subsystem: "com.example.yourapp", category: "general")
logger.log("This is a general log message")

logger.info("This is an info message")
logger.debug("This is a debug message")
logger.error("This is an error message")
```

### 4. Using debugPrint
debugPrint provides more descriptive output than print. It’s useful for debugging complex objects:

```swift
debugPrint("This is a debug log message")
```

### 5. Viewing Logs in Xcode
Open the Debug Console in Xcode by pressing ⌘ + ⇧ + C or by navigating to View > Debug Area > Activate Console.
All logs (print, NSLog, os_log) will appear here during app execution.


### Private vs public

console.app may show message as "private" when logging from os.Logger in xcode

- When logging messages using os.Logger in Xcode, the Console app may show some messages as "<private>" instead of the full content. This happens due to privacy protection in the unified logging system14. Here are the key points:
- By default, dynamic strings and complex objects are considered private and not collected automatically2.
- Private log messages are only fully visible when the Xcode debugger is attached. Otherwise, they appear as "<private>" in Console3.
- To make log messages public and visible in Console without the debugger attached, you need to explicitly mark them as public:

```swift
os_log("%{public}@", log: createOSLog(category: category), type: type, message)
```

Alternatively, you can use the Logger API introduced in iOS 14 and specify public access:

```swift
logger.log("Public message: \(value, privacy: .public)")
```

When using variables in log messages, they are redacted by default for privacy reasons.
To ensure your log messages are visible in Console without Xcode attached, make sure to mark sensitive data as private and non-sensitive data as public explicitly. This allows you to control what information is visible in logs while maintaining privacy for sensitive data.


### Best Practices
- Use os_log for production-level apps to take advantage of structured logging and filtering.
- Use print or debugPrint for quick debugging during development.
- Avoid leaving excessive logging in production code unless necessary for diagnostics. Use appropriate log levels (info, debug, error, etc.) to reduce clutter in the logs.

