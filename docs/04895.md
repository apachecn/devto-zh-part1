# Swift 中的实例属性与参数

> 原文：<https://dev.to/onmyway133/instance-property-vs-parameter-in-swift>

原帖[https://github.com/onmyway133/blog/issues/72](https://github.com/onmyway133/blog/issues/72)

前几天，我正在重构我代码。我有

```
extension MainController: TabBarViewDelegate {

  func buttonDidPress index: Int) {
    let initialIndex = tabBarView.selectedIndex
    let wholeAppContentView = updateWholeAppContentView()
    view.addSubview(wholeAppContentView)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

委托方法看起来不正确，因为很难区分必需的委托方法还是实例方法。它也缺少一个主题。我喜欢这个帖子 [API 设计](https://mattgemmell.com/api-design/)，你可以看章节`Rule 19: Always say who’s talking`

> 这是一个简单的规则，也是一个同样容易犯的错误。在您的委托方法中，始终将发送方作为参数传递。一直都是。即使是单身。即使是你想象不到的东西也会被同时使用不止一次。没有例外。

所以我重构了这个委托，并遵循它。

```
extension MainController: TabBarViewDelegate {

  func tabBarView(_ view: TabBarView, buttonDidPress index: Int) {
    let initialIndex = tabBarView.selectedIndex
    let wholeAppContentView = updateWholeAppContentView()
    view.addSubview(wholeAppContentView) // This is the culprit âš ï¸
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

即使`MainController.swift`中只有一行改变，整个 UI 也会中断，因为所有的视图都被添加到了标签栏中。奇怪的ðÿ˜。

没过多久我才想起来如果同名的话`parameter`优先于`instance property`。所以在这种情况下，编译器在没有警告的情况下，假设你正在处理来自`TabBarView`的`view`

这就是为什么你经常用`self`来消除歧义。

```
struct User: Codable, Equatable {
  let firstName: String
  let lastName: String

  init(firstName: String, lastName: String) {
    self.firstName = firstName
    self.lastName = lastName
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

回到我们的代码。解决方法是指定`self`来指定`MainController`的`view`或

```
self.view.addSubview(wholeAppContentView) 
```

Enter fullscreen mode Exit fullscreen mode

嗯，你可能会说，谁应该再添加视图以防标签栏更改ðÿ˜这是一个糟糕的例子，但ðÿ˜‡吸取了教训