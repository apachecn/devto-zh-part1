# 从故事板到 iOS 中的代码(第二集)

> 原文：<https://dev.to/jordanosterberg/from-storyboards-to-code-in-ios-episode-ii-59go>

**第二集**
**从故事板到 iOS 中的代码**

斯威夫特在乔丹·奥斯特伯格的 iOS 职业生涯中取代了故事板。Swift 主导了所有新项目，所有旧项目的故事板都被剥离，代之以代码。

但是有一个复杂的问题。自动布局代码很难编写，而且花费太多时间。

乔丹会做什么来继续战斗故事板和改善他的工作流程？

* * *

如果你到现在还没有意识到这一点，它来自星球大战。

让我们回顾一下我们在第一集“从故事板到 iOS 中的代码”中学到的内容([https://dev . to/osterberg Jordan/From-Storyboards-to-Code-in-iOS](https://dev.to/osterbergjordan/from-storyboards-to-code-in-ios))。

# 重述

1.  故事板对于学习如何制作 iOS 应用程序非常有用。

2.  一旦你使用了自动布局系统，故事板就失去了它的伟大之处，因为它已经完成了使用。

3.  故事板在大型项目中并不流行，需要很多故事板或者其他解决方案

4.  使用代码可以解决这些问题！

太好了。

让我们过渡到使用代码创建布局的问题。

# 问题与解决方案

1.  代码可能很庞大，水平跨度很大。这从来都不好。我们希望我们的代码是好的和可读的，而不是 huuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuge 文本字符串。(看到我在那里做了什么吗？)

2.  代码可能很难理解。无论您是新程序员还是从第一天起就使用自动布局的高级程序员，这都是很困难的。

3.  有很多棋子是必不可少的，忘记它们**会**打破你的布局。(例如`translatesAutoresizingMaskIntoConstraints`或`isActive`)

# 用解决方案解决问题

复杂的标题，我知道。但是好消息是我创造的解决方案并不复杂。我已经开始编写一个名为 EasyConstraints 的库，这是一个简单的 swift 文件中的`UIView`扩展。

让我们创建一个名为 redView 的视图。

```
let redView : UIView = {
    let view = UIView()
    view.backgroundColor = UIColor.red
    return view
}() 
```

Enter fullscreen mode Exit fullscreen mode

下面是一些自动布局代码，用于将 redView 放在我们的根视图的中心，并使其为 100x100。

```
self.view.addSubview(self.redView)

self.redView.translatesAutoresizingMaskIntoConstraints = false
self.redView.centerXAnchor.constraint(equalTo: self.view.centerXAnchor).isActive = true
self.redView.centerYAnchor.constraint(equalTo: self.view.centerYAnchor).isActive = true
self.redView.widthAnchor.constraint(equalToConstant: 100).isActive = true
self.redView.heightAnchor.constraint(equalToConstant: 100).isActive = true 
```

Enter fullscreen mode Exit fullscreen mode

太好了。现在让我们使用 EasyConstraints 来做同样的事情。

```
self.view.addSubview(self.redView)

self.redView.centerHorizontally(in: self.view)
self.redView.centerVertically(in: self.view)
self.redView.setWidth(equalTo: 100)
self.redView.setHeight(equalTo: 100) 
```

Enter fullscreen mode Exit fullscreen mode

我们结束了。更容易记忆和理解。该方法还返回一个`NSLayoutConstraint`，可以在示例项目中看到。

我想提一下，这是一个 **WIP，或者说正在进行的工作**库。并不是所有的自动布局约束都有简单易用的方法，我想添加的所有特性在这个版本中都没有。以下是目前所有方法的列表，我计划一有机会就添加一些:

当前方法:

*   水平居中| in: UIView
*   垂直居中|在:UIView
*   equalWidth | to: UIView
*   equalHeight | to: UIView
*   setHeight | equalTo: CGFloat
*   setWidth | equalTo: CGFloat
*   放置在下方|视图:UIView，距离:CGFloat
*   placeAbove | view: UIView，distance: CGFloat
*   placement right | view:ui view 距离:CGFloat
*   placeLeftOf | view: UIView 距离:CGFloat

计划的方法/特征:

*   将常量(CGFloat)参数添加到 equalWidth 和 equalHeight
*   向所有适用的方法添加修饰符(CGFloat)选项
*   将方法捆绑在一起，让生活变得更加简单
*   更多类似“放置”的方法，如 placeAbove 或 placeBelow

我确信我有更多的想法可以添加，我会更新这个列表。你可以在 https://github.com/JordanOsterberg/EasyConstraints 参观图书馆和它的发展。如果你有任何能让 EasyConstraints 变得更简单或“更酷”的建议或特性，请告诉我。