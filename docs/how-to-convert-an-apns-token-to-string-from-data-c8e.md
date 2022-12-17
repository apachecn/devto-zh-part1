# 如何将 APNs 令牌从“数据”转换为“字符串”?

> 原文：<https://dev.to/mono/how-to-convert-an-apns-token-to-string-from-data-c8e>

我认为这是将 APNs 令牌从`Data`转换为`String`的最佳代码。

```
let token = deviceToken.map { String(format: "%.2hhx", $0) }.joined() 
```

Enter fullscreen mode Exit fullscreen mode

### 用法

```
// Define initializer
extension String {
    public init(deviceToken: Data) {
        self = deviceToken.map { String(format: "%.2hhx", $0) }.joined()
    }
}

func application(_ application: UIApplication,
                 didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data!) {
    // Use like this:
    let token = String(deviceToken: deviceToken)
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

Full Japanese version: [如何在 Swift 中将推送通知用的 Device Token(Data 型)转换为十六进制字符串- Qiita](http://qiita.com/)