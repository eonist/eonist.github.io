My notes on FaceID and TouchID in swift<!--more-->

### FaceID resources:
- Nice intro https://www.hackingwithswift.com/read/28/4/touch-to-activate-touch-id-face-id-and-localauthentication
- Really good (covers a lot): https://www.techotopia.com/index.php/Implementing_TouchID_Authentication_in_iOS_8_Apps
- Massive resource: https://github.com/rushisangani/BiometricAuthentication

### Notify user:
```swift
func notifyUser(_ msg: String, err: String?) {
    let alert = UIAlertController(title: msg, message: err, preferredStyle: .alert)
    let cancelAction = UIAlertAction(title: "OK", style: .cancel, handler: nil)
    alert.addAction(cancelAction)
    self.present(alert, animated: true, completion: nil)
}
```

### Resources:
[https://github.com/sentryco/BioAuth](https://github.com/sentryco/BioAuth)
