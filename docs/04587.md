# iOS 本地通知忍者 bug！

> 原文：<https://dev.to/luisramos1337/ios-local-notifications-ninja-bug-1bee>

最近我在一个项目中工作，这个项目要求我显示一个本地通知。出于对 XCode 的热爱，该应用程序不会显示警告！我不知所措！

从 iOS 10 开始，iOS 通知框架进行了改进，统一了远程和本地通知。这使我在试图解决问题时陷入了困境。我唯一要做的就是在向 UNUserNotificationCenter 添加通知时弹出一条错误消息:

```
Adding notification request failed with error: Error Domain=NSCocoaErrorDomain Code=4097 "connection to service named com.apple.usernotifications.usernotificationservice" UserInfo={NSDebugDescription=connection to service named com.apple.usernotifications.usernotificationservice} 
```

多么神秘的消息，感谢苹果开发团队！我有一个自定义的动作设置，所以用户可以打开一个包含更多信息的 url。我将这个 URL 添加到通知的`userInfo`中，这里就是 bug 所在的地方！问题是我添加了 url 对象，而不是 URL 的绝对字符串。我一改这个，一切都正常了。

当然，一个 [stackoverflow 答案](https://stackoverflow.com/questions/41360531/unmutablenotificationcontent-with-custom-object-in-userinfo)帮我搞清楚了这一点！