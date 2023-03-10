# 介绍页面——管理页面的组件约定

> 原文：<https://dev.to/michaeljota/introducing-pages---a-component-convention-to-manage-pages-4b4>

# 前情提要...

*   [小工具介绍](https://dev.to/michaeljota/widgets-and-components-for-angular-2-and-up)
*   [输入-在微件中设置“模式”](https://dev.to/michaeljota/input-set-pattern-for-widgets-in-angular)
*   [窗口小部件中的只读原语](https://dev.to/michaeljota/readonly-inputs-for-primitives-properties-in-widgets-for-angular)

# 和现在...

好了，男孩们，女孩们，另一个认为你可以决定意大利面是米饭的人只是因为它这样定义自己，现在我已经发表了我管理组件的两种方法。我目前将我的 angular 组件分为三类，基于它们关注的是什么，小部件，管理它们的外观，组件，管理它们的行为，正如我今天将向你介绍的，页面，管理好页面。

# 什么是*页面*？

一页是这样的。你在看什么，用户就会看什么。大多数时候，组件不会被它的`selector`引用，而是被某个 url 上的路由器引用。这是页面组件和常规组件的第一个区别。

*   一个页面可以包含其他页面

毫无疑问。但是，它们都应该由一个 url 状态来表示，仅此而已。它们永远不应该被它的`selector`引用，因为那样我就不是一个页面了，不是吗？

*   一个页面不应该有`selector`

如果`@Component`装饰者没有`selector`，他们就不太可能引用这样的页面。我认为不这样做更安全。

*   页面不应该有行为

就像我们的 AppComponent 一样，或者无论你在哪里调用应用程序的入口组件，那里的逻辑应该是最少的。大多数 AppComponent 将行为委托给它们的内部组件、路由器和服务。

*   一个页面只能有组件

好吧，你终究要表现出点什么。但是，为什么只有组件？嗯，如果你把一个小部件放在那里，他们更有可能把那个小部件的逻辑放在你的页面里面，而它不应该在那里。

*   一个页面应该有端到端的测试

这是你的用户将会互动，所以我认为这是逻辑，把你的 e2e 放在一边。如果您的组件旁边应该有单元测试，并且您的小部件应该...我认为他们也应该有一个单元测试，那么你的页面应该只包含 e2e 测试。毕竟你测试了你所有的组件。对吗？对吗？因为我不知道。

# 一页

如果没有例子可以复制粘贴，生活会是什么样子？好吧，对不起，这太没用了，不能复制粘贴。但是，我仍然希望你记住这个想法。

```
import { Component } from '@angular/core';

@Component({
  template: `
    <my-add-todo></my-add-todo>
    <my-visible-todo-list></my-visible-todo-list>
    <my-filter-buttons-widget></my-filter-buttons-widget>
  `,
})
export class TodoPage {} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们可以看到 todo 页面是怎样的。当您导航到`/todo` url 时，您将确切地知道会发生什么，只有您先前定义的组件，以及您放入其中的行为。

# 谢谢！

一如既往，我希望你喜欢这篇文章，我非常感谢你读完了它。如果你有关于它的评论，想法或者只是想说这不应该是这样的，请使用下面的评论部分。我也期待着阅读你的作品。