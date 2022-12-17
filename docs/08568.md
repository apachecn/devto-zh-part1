# 啊啊！！-宏、解析转换和其他@ Erlang 战场

> 原文：<https://dev.to/elbrujohalcon/aaargh---macros-parse-transforms-and-others--erlang-battleground>

不久前，[约翰·休斯](https://twitter.com/rjmh?lang=es)展示了一个叫做*的模块 Aaargh！！*到[二郎腿](http://erlang.org/pipermail/erlang-questions/2017-January/091550.html)邮件列表。他展示了 parse 转换、宏和 Erlang 编译器是如何一起给我们带来麻烦的。我将在这里展示同样的故事。如果你已经读过了，你可以放心地跳过文章的其余部分。

## 怪异模块

这基本上是 John 发送给邮件列表的模块(我更改了它的名字，这样更容易避免撇号)…

```
 -module(weird).

    -define(PLEASE_DONT).

    -ifdnef(PLEASE_DONT).
    -compile({parse_transform, undefined_parse_transform}).
    -endif. 
```

Enter fullscreen mode Exit fullscreen mode

这个想法是，我们首先在一行中定义`PLEASE_DONT`宏，如果需要的话，我们可以稍后注释掉(或者更确切地说，在编译时删除并定义这个宏)。

然后，如果宏是**而不是**定义的，我们通过一个名为`undefined_parse_transform`的解析转换来运行代码。这里的重点是这个解析转换模块不存在，所以这一行*应该* *不编译*。

但是宏*是*定义的，所以不应该使用解析转换，对吧？

```
 $ erlc weird.erl
    src/weird.erl: undefined parse transform 'undefined_parse_transform' 
```

Enter fullscreen mode Exit fullscreen mode

## 还有我的宏？

原来宏是**而不是**定义的。正如亚历克斯在邮件列表中指出的那样…

> # There is no argument definition ()

正如你在[中看到的，文档](http://erlang.org/doc/reference_manual/macros.html#id85572):

> 一个宏定义如下:
> *-define(Const，Replacement)。
> -定义(Func(Var1、...、VarN)、替换)。*

如果我们改变代码中的定义行…

```
 -module(weird).

    -define(PLEASE_DONT, true).

    -ifndef(PLEASE_DONT).
    -compile({parse_transform, undefined_parse_transform}).
    -endif. 
```

Enter fullscreen mode Exit fullscreen mode

…它可以完美地编译:

```
 $ erlc weird.erl
    $ ls weird.beam
    weird.beam 
```

Enter fullscreen mode Exit fullscreen mode

## 你为什么不早告诉我？

就这样，对吧？嗯……没那么容易。如果没有单参数 define()，那么为什么编译器/解析器没有警告我们呢？看看这个:

```
 -module(weird).

    -define(PLEASE_DONT). 
```

Enter fullscreen mode Exit fullscreen mode

如果我们试图编译那个模块…

```
 $ erlc weird.erl
    src/weird.erl:3: badly formed 'define' 
```

Enter fullscreen mode Exit fullscreen mode

## 这是怎么回事？

我们这里有两种不同的*编译*错误:

*   “定义”属性的格式不正确

*   不存在的解析转换

我们只看到其中一个，因为它们是在编译过程的不同阶段发现的。为了更好地理解这一点，你可以在下面找到一段理查德·卡尔森的演讲视频，其中详细介绍了编译器和它的朋友们。但是简而言之:对于*解析器*，单参数定义(或者任何其他属性声明，无论如何)完全有效，即使它能够识别宏定义，但是缺少解析转换模块就不行。*编译器*是检测单参数定义错误的编译器。*解析器*在*编译器*之前执行，在这两者之间，只有第一个发现错误的解析器会大声疾呼；)

这就是为什么当 define 被破坏时，*解析器*会忽略它并继续进行，直到它发现一个丢失的解析转换模块，然后它发出错误并停止进程。但是当解析器没有发现问题时(也就是说，在我们的例子中，没有缺少解析转换模块)，那么*编译器*被执行，它发现一个参数定义并且失败。

* * *

## 奖金追踪

在这个关于 erlang-question 的谈话结束后，我的脑海中出现了一个额外的问题:

> 如果没有单参数 define()，那么如果我在命令行中定义一个宏，会发生什么呢？

为了测试这一点，我创建了这个模块:

```
 -module(weird).

    -export([macro/0]).

    macro() -> ?THE_MACRO. 
```

Enter fullscreen mode Exit fullscreen mode

我完全可以编译模块，并在命令行上为宏指定一个值，就像这样:

```
 $ erlc -DTHE_MACRO=hello weird.erl
    $ 
```

Enter fullscreen mode Exit fullscreen mode

如果我尝试在 Erlang shell 中使用它，我会得到下面的预期结果:

```
 1> weird:macro().
    hello
    2> 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我不为宏指定一个值，会发生什么？文件在这方面有点模糊

> -D
> 定义一个宏。

我们自己试试，好吗？

```
 $ erlc -DTHE_MACRO weird.erl
    $ 
```

Enter fullscreen mode Exit fullscreen mode

既然模块已经编译了，Erlang 编译器应该已经给宏赋值了，对吗？让我们看看这个值是多少…

```
 1> weird:macro().
    true
    2> 
```

Enter fullscreen mode Exit fullscreen mode

[![I don’t know what I expected](img/f5a7e193aed07ceb1e00455d7fc49692.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uMmyVK5g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i0.kym-cdn.com/photimg/newsfeed/000/617/851/56d.gif)

* * *

*本文原载于[中](https://medium.com/erlang-battleground/aaargh-a7dc940f8d0f)*