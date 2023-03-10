# 将实时 REPL 功能引入开发人员帖子

> 原文：<https://dev.to/ben/bringing-live-repl-functionality-to-devto-posts>

我一直致力于在帖子中添加 [Klipse](https://github.com/viebel/klipse) live REPL 功能。这并不是每个人都可以使用的，但是这是第一篇有这种功能的文章。ðŸ˜Š

例如，这里有一个 JavaScript REPL。

```
 [1,2,3].map((x) => (x * 10) + 1 ) 
```

Enter fullscreen mode Exit fullscreen mode

我还在整理细节，所以这个功能现在只能在我的账户上使用，但是很快就会为所有人推出。对于某些可以利用交互性进行演示和学习的帖子来说，这是一个很好的工具。执行此功能所需的代码只会出现在使用此功能的页面上。

下面是和上面一样的地图函数，但是用的是 Ruby:

```
 [1,2,3].map { |x| (x * 10) + 1 } 
```

Enter fullscreen mode Exit fullscreen mode

非常感谢 [Yehonathan Sharvit](https://dev.to/viebel) ，Klipse 的主要维护者[提出了这个集成](https://github.com/thepracticaldev/dev.to/issues/9)。

非常欢迎反馈