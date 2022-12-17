# 仅观察通知一次

> 原文：<https://dev.to/masakihori/observing-notification-only-once-2h3>

```
extension NotificationCenter {

    func addObserverOnce(forName name: NSNotification.Name?, object obj: Any?, queue: OperationQueue?, using block: @escaping (Notification) -> Void) {

        weak var token: NSObjectProtocol?
        token = addObserver(forName: name, object: obj, queue: queue) {notification in

            token.map(self.removeObserver)
            block(notification)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode