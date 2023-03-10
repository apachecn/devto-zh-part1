# 不平衡括号

> 原文：<https://dev.to/elbrujohalcon/unbalanced-parentheses>

## …或者其他不使用宏的原因

这是一篇关于我在 2017 年旧金山的[二郎&仙丹工厂*现场*展示的东西的文章。如果您喜欢 Erlang 中的模块属性，那么您可以做什么，不可以做什么。](http://www.erlangelixir.com/brujo-benavides.html)

[![Golden Gate Park, from GrandView Park, SF](img/9d4910d5048d2a4302150cebc3bee02d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TV-ldFkI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ArcTFoMlMmoqJS3ZfU1-f7Q.jpeg)

### 备注

首先，[费德里科](https://medium.com/@unbalancedparen):抱歉盗用你的名字。这实在是太酷了。)
话虽如此，这是整个演讲的视频，如果你想看的话…

如果你喜欢西班牙语…

好了，链接够了！言归正传……

* * *

### 平常的疑点

这是一个非常有效的 Erlang 模块:

```
 -module(my_mod).
    -export([whoami/0]).

    -spec whoami() -> atom().
    whoami() -> my_mod. 
```

Enter fullscreen mode Exit fullscreen mode

你可以编译它并执行它的功能…

```
 1> c(my_mod).
    {ok,my_mod}
    2> my_mod:whoami().
    my_mod
    3> 
```

Enter fullscreen mode Exit fullscreen mode

这也是一个非常有效的 Erlang 模块(现在使用一个宏):

```
 -module(my_mod).
    -export([whoami/0]).

    -spec whoami() -> atom().
    whoami() -> ?MODULE. 
```

Enter fullscreen mode Exit fullscreen mode

不出所料，同样的结果…

```
 1> c(my_mod).
    {ok,my_mod}
    2> my_mod:whoami().
    my_mod
    3> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 酷家伙

但是……那里的 **spec** 属性没有括号。我们还可以去掉其他属性的括号吗？T2 的医生对此不太清楚。它们显示了以下属性的通用语法:

```
-Tag(Value). 
```

Enter fullscreen mode Exit fullscreen mode

但是还有像*规格*、*类型*或*回调*这样的属性，显示时没有括号。那么，为什么不试试呢？如果括号周围没有圆括号，导出会好得多，对吗？

```
 -module my_mod.
    -export [whoami/0].

    -spec whoami() -> atom().
    whoami() -> my_mod. 
```

Enter fullscreen mode Exit fullscreen mode

真的有用！！看那个…

```
 1> c(my_mod).
    {ok,my_mod}
    2> my_mod:whoami().
    my_mod
    3> 
```

Enter fullscreen mode Exit fullscreen mode

[![YEY!! (from Primo GIF)](img/aae53d528023b68c7334464d3e64b57d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8TJL11oz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A0NffgULjv2vhlMlhpc4xCg.gif)

我再也不在那里写括号了！

* * *

### 没那么快，牛仔！

嗯…我不是宏的粉丝，但有时我最终会使用预定义的宏，就像我对*做的那样？模块*高于*……*

```
 -module my_mod.
    -export [whoami/0].

    -spec whoami() -> atom().
    whoami() -> ?MODULE. 
```

Enter fullscreen mode Exit fullscreen mode

现在如果你尝试编译那个模块，你会发现一个意想不到的结果…

```
 1> c(my_mod).
    my_mod.erl:5: 
    my_mod.erl:2: function whoami/0 undefined
    my_mod.erl:4: spec for undefined function whoami/0
    error 
```

Enter fullscreen mode Exit fullscreen mode

[![WAT (from Primo GIF)](img/f72aba04ce921c612e1ab3a0b8fff088.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OpgIL6q9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2AqOekxkFDrnQQIekBjkouiQ.gif)

* * *

### 这是怎么回事？

这次我真的没有线索了。看得出来，为了用宏*？模块*你必须用圆括号将你的模块名括起来。但是我没有做过任何研究来找到它的根源。这主要是因为我有一种非常独特的感觉，它将把我带到解析器、编译器等领域。那是我喜欢远离的地方。

所以，对于这篇文章，亲爱的读者，如果你有足够的勇气深入到 Erlang 编译器和它的朋友们的领域，并了解正在发生的事情，请在下面的评论中告诉我。

当然，如果您可以通过向 [erlang/otp](https://github.com/erlang/otp) 发送一个 Pull 请求来解决这个问题…

[![Office Space (1999)](img/c6801b5e5e8f0d224f7cfb6f80f09ac7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BZ2ibUyN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AcB1elrj2xoC7XFZZ_XnrVQ.jpeg)

* * *

本文首发于 [*二郎战场*](https://medium.com/erlang-battleground) 为 [**不平衡括号**](https://medium.com/erlang-battleground/unbalanced-parentheses-6634140c6adb) 。