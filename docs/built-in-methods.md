# 内置方法

> 原文：<https://dev.to/rpalo/built-in-methods>

这将是一个快速的过程。最近，我在飞机上做一些事情(我将在接下来的文章中讨论)。我用的是红宝石。显然，我不会为机上互联网接入支付整整五美元，所以我过得很艰难。我遇到了一个错误。

```
if line.contains? "tags: "
    # Do stuff 
```

Enter fullscreen mode Exit fullscreen mode

砰。该死！我刚刚完成 Python 编程，有一段时间没有用 Ruby 编程了，所以我重写了它。

```
if "tags: " in line
    # Do stuff 
```

Enter fullscreen mode Exit fullscreen mode

没有。`Unexpected keyword_in`。呸！我知道这里有一种方法，但我怎么也想不起是什么方法了。我的第一反应是立即搜索 Ruby String 文档，但是——记住——没有互联网。出于希望和绝望，带着对编码神的快速祈祷，我在终端输入了以下内容:

```
$ irb
# Typical IRB headers
>>> "Potato".methods
[:include?, :%, :*, :+, :unicode_normalize, :to_c, :unicode_normalize!, :unicode_normalized?, :count, ... 
```

Enter fullscreen mode Exit fullscreen mode

那里！我所需要的就在前面。不需要谷歌搜索。所以下次你在谷歌上问问题的时候，先问问你的代码吧！

```
if line.include? "tags: "
    # profit
end 
```

Enter fullscreen mode Exit fullscreen mode

> 如果您感兴趣，您可以通过`dir("potato")`在 Python 中做同样的事情。

*最初发布自[我的博客](http://assertnotmagic.com)。*