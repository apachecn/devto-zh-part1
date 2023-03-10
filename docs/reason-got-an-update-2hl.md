# 原因更新了

> 原文：<https://dev.to/kayis/reason-got-an-update-2hl>

*Flickr 上 [GotCredit](https://www.flickr.com/photos/gotcredit/) 的封面图片，由我裁剪。*

正如你可能听说的那样，脸书正在函数式编程语言上做自己的事情。他们创造了 Reason，一种具有 OCaml 能力的语言，但更像 JavaScript 的语法。

现在，这种新语言已经升级到第 3 版，其中有很多语法更新。

```
 let myFunction myArgument => ... 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
 let myFunction = (myArgument) => ... 
```

Enter fullscreen mode Exit fullscreen mode

我认为从 JS 那里听起来更理智一些。旧版本不知何故看起来有点像常规函数定义和箭头函数定义的混合，其中使用了`let`而不是`function`，都没有括号。

此外，函数调用站点也有变化，每个人都有更多的括号！

```
 myFunction "Hello"; 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
 myFunction("Hello"); 
```

Enter fullscreen mode Exit fullscreen mode

早在 2015 年之前，我在 LiveScript 中做过一些项目，并且越来越喜欢最小语法，但是我不得不承认，括号通常有助于使事情变得更清楚。

箭头功能也得到了更新。

```
 {
      add: fun a b => a + b
    } 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
 {
      add: (a, b) => a + b
    } 
```

Enter fullscreen mode Exit fullscreen mode

这又是一个品味问题，但是如果你懂 JavaScript，你会在这里感觉很自在。

然后是命名的参数。这有点像你在 JavaScript 中创建一个函数，它接受一个对象而不是多个参数，所以你不必坚持参数*和*的位置，在调用站点上查看每个参数被调用的内容。

```
 let myFunction ::url ::method => ... 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
 let myFunction = (~url, ~method) => ... 
```

Enter fullscreen mode Exit fullscreen mode

在通话网站上也进行了更改。

```
 myFunction ::url="http://dev.to" ::method="POST"; 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
 myFunction(~url="http://dev.to", ~method="POST"); 
```

Enter fullscreen mode Exit fullscreen mode

字符串串联现在是`++`所以

```
 "abc" ^ "def" 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
 "abc" ++ "def" 
```

Enter fullscreen mode Exit fullscreen mode

不同于 JavaScript 版本，但我猜更接近于`+`。

求反操作符也得到了更像 JavaScript 的表示

```
 not expression; 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
 ! expression; 
```

Enter fullscreen mode Exit fullscreen mode

同样，调用一个函数而不需要参数来传递它`()`，这看起来有点像在 JavaScript 中调用它，尽管这个概念似乎有点不同，但是在函数名和`()`
之间有一个空格

```
 myFunction (); 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
 myFunction(); 
```

Enter fullscreen mode Exit fullscreen mode

一个微小的变化，但这些看似无用的小空间可能会让人们紧张。

## 结论

Reason 越来越多地从 OCaml 转移到 JavaScript，因此从它开始从未如此容易。许多人抱怨去掉了很酷的 OCaml 语法，用 JavaScript 垃圾把它弄得乱七八糟，但是我认为这种语法融合是必须采用的。

此外，OCaml 不会消失，我已经听说人们开始使用 Reason，后来改用 OCaml，因为他们发现这种语法更轻量级。