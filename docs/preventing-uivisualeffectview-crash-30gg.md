# 防止 UIVisualEffectView 崩溃

> 原文：<https://dev.to/onmyway133/preventing-uivisualeffectview-crash-30gg>

原帖[https://github.com/onmyway133/blog/issues/124](https://github.com/onmyway133/blog/issues/124)

我们都知道 iOS 11 上的`UIVisualEffectView`可能会崩溃。修复方法是不直接添加子视图到`UIVisualEffectView`，而是添加到它的`contentView`。所以我们应该改变

```
effectView.addSubview(button) 
```

Enter fullscreen mode Exit fullscreen mode

至

```
effectView.contentView.addubView(button) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们不需要进行 iOS 版本检查，因为`effectView.contentView`适用于任何 iOS 版本。

### 潜在的崩溃案例

以下是一些可能导致崩溃的情况

***奇怪的命名***

通常我们把自己的`UIVisualEffectView`命名为`blurView`、`effectView`。但是有时候我们会用不同的名字来命名，比如`navigationView`、`containerView`、`boxView`，...这样我们可能会完全忘记它是一个`UIVisualEffectView`🙀

```
containerView.addSubview(button)
boxView.insertSubview(label, at: 0) 
```

Enter fullscreen mode Exit fullscreen mode

***自定义加载视图***

有时把我们的`UIViewController 's view`作为一个整体模糊视图是很方便的，这样里面的所有东西都有一个漂亮的模糊效果背景

```
class OverlayController: UIViewController {
  let blurView = UIVisualEffectView(effect: UIBlurEffect(style: .dark))
  override func loadView() {
    super.loadView()
    self.view = blurView
  }

  override func viewDidLoad() {
    super.viewDidLoad()

    view.addSubview(button)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过将我们的`blurView`设置为`loadView`中的`view`，我们事后并不知道`view`实际上是一个`UIVisualEffectView`🙀

***传承***

如果我们有另一个继承自我们的`OverlayController`的`UIViewController`会发生什么，它只知道`view`是`UIView`，它不知道它是一个伪装的`UIVisualEffectView`🙀

```
class ClocksController: OverlayController {
  override func viewDidLoad() {
    super.viewDidLoad()

    view.addSubview(timeLabel)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

***超类类型***

有时用协议或超类类型来声明我们的东西。我们 API 的消费者没有线索知道它是`UIVisualEffectView`🙀

```
let view: UIView = UIVisualEffectView(effect: UIBlurEffect(style: .dark)) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，在我们看来`view`是类型`UIView`

***遗留代码库***

现在假设您已经处理了一个遗留代码库。找到并替换所有与`UIVisualEffectView`相关的东西是非常困难的任务。尤其是因为我们倾向于为 UI 编写更少的测试

### 使其不可能崩溃

我喜欢像[幻影类型](https://wiki.haskell.org/Phantom_type)这样的概念来限制接口。这里我们没有使用类型，而是使用了包装器

```
final class BlurView: UIView {
  private let effectView: UIVisualEffectView

  init(style: UIBlurEffectStyle, backgroundColor: UIColor? = nil) {
    self.effectView = UIVisualEffectView(effect: UIBlurEffect(style: style))
    self.effectView.backgroundColor = backgroundColor
    super.init(frame: .zero)
    insertSubview(effectView, at: 0)
  }

  required init?(coder aDecoder: NSCoder) {
    fatalError()
  }

  override func addSubview(_ view: UIView) {
    effectView.contentView.addSubview(view)
  }

  override func layoutSubviews() {
    super.layoutSubviews()

    effectView.frame = bounds
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们覆盖了`addSubview`来总是添加视图到`effectView.contentView`。在`init`方法中，我们需要调用`insertSubview`，因为我们覆盖了`addSubview`

现在`BlurView`由于是底层`UIVisualEffectView`而有了模糊效果，但是因为它的`UIView`接口而只暴露了`addSubview`。这样就不可能导致崩溃😎

```
let blurView = BlurView(style: .dark)
blurView.addSubview(button( 
```

Enter fullscreen mode Exit fullscreen mode