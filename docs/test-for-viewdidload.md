# 测试 viewDidLoad

> 原文：<https://dev.to/onmyway133/test-for-viewdidload>

原帖[https://github.com/onmyway133/blog/issues/52](https://github.com/onmyway133/blog/issues/52)

假设我们有下面的视图控制器

```
class ListController: UIViewController {
  override func viewDidLoad() {
    super.viewDidLoad()
    view.backgroundColor = .white
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 结识`viewDidLoad`

我们知道第一次创建视图时会调用`viewDidLoad`。所以在单元测试中，如果你用`viewDidLoad`来触发，你就会落入
的陷阱

```
func testSetup() {
  let controller = ListController()
  controller.viewDidLoad()
} 
```

Enter fullscreen mode Exit fullscreen mode

### 为什么`viewDidLoad`叫两次？

*   它在测试中被调用一次
*   在您的`viewDidLoad`方法中，您访问第一次创建的`view`，因此它将再次触发`viewDidLoad`

### 正确的做法

最佳实践不是自己触发事件，而是做一些事情让事件发生。在单元测试中，我们只需访问`view`来触发`viewDidLoad`

```
func testSetup() {
  let controller = ListController()
  let _ = controller.view
} 
```

Enter fullscreen mode Exit fullscreen mode