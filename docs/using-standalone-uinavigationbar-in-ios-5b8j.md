# 在 iOS 中使用独立的 UINavigationBar

> 原文：<https://dev.to/onmyway133/using-standalone-uinavigationbar-in-ios-5b8j>

原帖[https://github.com/onmyway133/blog/issues/144](https://github.com/onmyway133/blog/issues/144)

有时候，我们希望同一个`UIViewController`在以模态方式呈现或从`UINavigationController`堆栈中推出时看起来不错。看看条形码扫描仪和 https://github.com/hyperoslo/BarcodeScanner/pull/82 的公关

当它出现时，我们需要一个标题视图，这样我们就可以显示一个标题和一个关闭按钮。我们可以创建一个从`UIView`继承而来的自定义`HeaderView`，或者在呈现之前将其嵌入到`UINavigationController`中。

如果我们使用嵌入在`UINavigationController`方法中的控制器，当它被推动时会与另一个`UINavigationController`碰撞。

如果我们使用 custom `HeaderView`，那么我们需要对视图进行布局，以便它在纵向和横向上都看起来不错，在 iPhone X 上看起来也不错。

## 使用独立的 UINavigationBar

因为`UINavigationController`在幕后使用`UINavigationBar`，而后者使用`UINavigationItem`信息来呈现内容。我们可以通过使用独立的`UINavigationBar`来模仿这种行为。参见[向独立导航栏添加内容](https://developer.apple.com/documentation/uikit/uinavigationbar)

> 在绝大多数情况下，您将使用导航栏作为导航控制器的一部分。但是，在某些情况下，您可能希望使用导航栏 UI 并实现自己的内容导航方法。在这些情况下，您可以使用独立的导航栏。
> 
> 导航栏管理器是 uinavigationitem 对象的堆栈

美妙的是我们的独立`UINavigationBar`和`UINavigationController`是一样的，使用相同的`UINavigationItem`，不需要手动布局

### 申报`UINavigationItem`

我们可以像普通的`navigationItem`
一样设置属性

```
let standaloneItem = UINavigationItem()
standaloneItem.leftBarButtonItem = UIBarButtonItem(customView: closeButton)
standaloneItem.titleView = UILabel() 
```

Enter fullscreen mode Exit fullscreen mode

### 添加`UINavigationBar`

定制你的工具条，然后声明布局约束。你只需要在左、右、上钉上。请注意，您需要实现`UINavigationBarDelegate`来将工具条附加到状态栏上，这样它在 iPhone X 上看起来也很好

```
let navigationBar = UINavigationBar()
navigationBar.isTranslucent = false
navigationBar.delegate = self
navigationBar.backgroundColor = .white
navigationBar.items = [standaloneItem]

navigationBar.translatesAutoresizingMaskIntoConstraints = false
navigationBar.leftAnchor.constraint(equalTo: view.leftAnchor).isActive = true
navigationBar.rightAnchor.constraint(equalTo: view.rightAnchor).isActive = true

if #available(iOS 11, *) {
  navigationBar.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor).isActive = true
} else {
  navigationBar.topAnchor.constraint(equalTo: view.topAnchor).isActive = true
} 
```

Enter fullscreen mode Exit fullscreen mode

```
extension BarcodeScannerController: UINavigationBarDelegate {
  public func position(for bar: UIBarPositioning) -> UIBarPosition {
    return .topAttached
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 内参`UINavigationController`

当这个`UIViewController`从一个`UINavigationController`栈中被推出时，我们只需要隐藏我们独立的`navigationBar`。如果我们更喜欢默认的后退按钮，我们就不需要设置`leftBarButtonItem`

```
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)

  if navigationController != nil {
    navigationItem.titleView = standaloneItem.titleView
    navigationBar.isHidden = true
  } else {
    navigationBar.isHidden = false
  }
} 
```

Enter fullscreen mode Exit fullscreen mode