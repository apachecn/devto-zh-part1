# 博客图标

> 原文：<https://dev.to/adamkdean/blogger-icons-3j7f>

关于 blogger，或者说许多使用它的 blogger，有一点让我印象深刻，那就是我从来没有见过一个博客没有使用默认的 blogger favicon。现在有些人可能不会太在意，一个 16x16 的小图标到底有多重要？

如果你像我一样，这很重要。我用 Chrome，事实上我靠 Chrome 生活，我的书签栏简直就是一大串图标。没有文字，只有图标。因此，当我有一个最喜欢的博客列表时，我不得不开始猜测哪个是哪个。

blogger 中有些东西很难破解，但幸运的是 favicon 不是。简单地将这一行稍加修改后放在 title 标签下面，就万事俱备了。我以前从未使用过`type="image/vnd.microsoft.icon"`，但它似乎对 blogger 很有用！

```
<link href="http://domain.com/iconnane.ico" rel="shortcut icon" type="image/vnd.microsoft.icon"></link> 
```

Enter fullscreen mode Exit fullscreen mode

...还有 tada！你现在正在帮助图标书签的世界成为一个更快乐的地方！