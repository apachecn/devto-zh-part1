# 委托 RxSwift

> 原文：<https://dev.to/onmyway133/delegate-with-rxswift>

我们可以用 RxSwift 用`DelegateProxy`和`DelegateProxyType`做出漂亮的 delegate。但是在其他一些情况下，我们可以用`PublishSubject`创建一个自定义类。

这就是我们如何从`UIApplication`生命周期事件
中制造`rx`

```
class LifeCycle {
  let didEnterBackground = PublishSubject<Void>()
  let willEnterForeground  = PublishSubject<Void>()
  let didBecomeActive = PublishSubject<Void>()
  let willResignActive = PublishSubject<Void>()

  init() {
    let center = NotificationCenter.default
    let app = UIApplication.shared

    center.addObserver(forName: Notification.Name.UIApplicationDidEnterBackground,
                       object: app, queue: .main, using: { [weak self] _ in
      self?.didEnterBackground.onNext(())
    })

    center.addObserver(forName: Notification.Name.UIApplicationWillEnterForeground,
                       object: app, queue: .main, using: { [weak self] _ in
      self?.willEnterForeground.onNext(())
    })

    center.addObserver(forName: Notification.Name.UIApplicationDidBecomeActive,
                       object: app, queue: .main, using: { [weak self] _ in
      self?.didBecomeActive.onNext(())
    })

    center.addObserver(forName: Notification.Name.UIApplicationWillResignActive,
                       object: app, queue: .main, using: { [weak self] _ in
      self?.willResignActive.onNext(())
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

```
let lifeCycle = LifeCycle()
lifeCycle.didBecomeActive
  .bindNext({ [weak self] in
    self?.viewModel.reloadProfile()
  })
  .disposed(by: bag) 
```

Enter fullscreen mode Exit fullscreen mode