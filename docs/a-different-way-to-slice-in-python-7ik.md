# python 中不同的切片方式

> 原文：<https://dev.to/tuxbsd/a-different-way-to-slice-in-python-7ik>

一个非常有趣的话题出现在我当地的社区 slack 频道。有人问如何使用对象进行切片。起初，我不确定他们在谈论什么，为什么下面的内容对他们不起作用。

```
mylist[:5] 
```

Enter fullscreen mode Exit fullscreen mode

快速搜索后，有人在 python 文档中找到了这个参考文献。这是一本好书，但我还是不太明白为什么。然后我突然想到。我记得 C 语言有一个叫宏的东西，我当时想，哦，嘿，这是一个很酷很简单的创建切片宏的方法。

例如，你可以有

```
SL5 = slice(5)

my_new_list = my_list[SL5]
my_other_new_list = my_other_list[SL5] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们创建了两个新的列表，前五个条目在我的列表和我的其他列表中。我仍然没有找到所有可能有用的方法，但是我很高兴我能够看到一个新的概念，并在几分钟内找到它的好用途。

PS。抱歉，这篇文章写得很粗糙。除了我的介绍，这是我在这里的第一个真正的帖子。内容创作还是我的弱项。