# 我是如何意外地编写了一个很棒的 HTML 预处理器的

> 原文：<https://dev.to/nektro/how-i-accidentally-wrote-an-awesome-html-preprocessor-995>

作为一名程序员，编译器在我看来总是像一个百万行的黑匣子，只有制作一个操作系统才会被吓倒。但是艰难的挑战是最好的挑战，所以不久前我开始尝试挑战自己。

好的。

如果你想写一个编译器，主要有三个部分。词法分析器、解析器和代码生成器。我已经用包括 Java 和 C#在内的各种语言开始了这个项目，但我的成功实现目前是在 JavaScript 中。

## ①乐兴

词法分析的过程相对于这个过程的其他部分来说是非常简单的。考虑下面的代码:

```
const hello = "Hello, " + "World!";
const sum = 4 + 5; 
```

Enter fullscreen mode Exit fullscreen mode

当对一段代码进行词法分析时，您必须遍历整个源代码，并将字符串转换为一组标记。令牌是存储一小部分源代码信息的简单结构。对于我编写的 lexer，我使用了四种主要的令牌类型:`Keyword`、`Word`、`String`和`Symbol`。所以上面的代码在 lexing 之后可能看起来像这样:

```
Keyword<"const">
Word<"hello">
Symbol<"=">
String<"Hello, ">
Symbol<"+">
String<"World">
Symbol<";">
Keyword<"const">
Word<"sum">
Symbol<"=">
Word<"4">
Symbol<"+">
Word<"5">
Symbol<";"> 
```

Enter fullscreen mode Exit fullscreen mode

如果你已经做到了这一步，那么太棒了！

我的项目 Mantle 通过一个你可以扩展的名为`mantle.lexer.Lexer`的抽象类来完成这个超级任务。您只需定义一个关键字、符号和字符串分隔符的列表，告诉它是否允许注释，并传递一个定义某个字符是否可以在单词中使用的函数。在那之后，创建上面的列表变得像调用`Lexer.parse()`一样容易，但是继续下去，你几乎不会自己调用`parse()`。

更多关于地幔的信息可以在 https://github.com/Nektro/mantle.js 找到

## 2)解析

这是最难的部分。

解析要求您找出可以将令牌列表压缩到单个节点中的令牌模式。这需要大量的试验和错误才能得到正确的结果，这也是这个项目耗时如此之久的主要原因。

例如，对于上面的代码，我们可以定义以下规则:

```
Add <= String + String
Add <= Integer + Integer
AssignmentConst <= const Word = Add
StatementList <= Add Add 
```

Enter fullscreen mode Exit fullscreen mode

我很快就发现，越是复杂的规则，越是复杂的语言。

`mantle.parser.Parser`的 JSON 示例可以在[https://github . com/Nektro/mantle . js/blob/master/langs/mantle-JSON . js](https://github.com/Nektro/mantle.js/blob/master/langs/mantle-json.js)找到

## 3)代码生成

这是遍历最终压缩节点(也称为抽象语法树)的过程，并对它们进行排序，直到得到新的输出。

> 注意:
> 高级语言的优化需要比调用 toString()更多的工作，但这超出了我的范围

# 4) Corgi -我的新 HTML 预处理器

此时我欣喜若狂。我成功的做了一个 JSON 解析器。但是我想让事情变得更复杂。所以我转向了 HTML。问题是，HTML 的格式不是很好。所以我想我应该做一个更容易被 Mantle 解析的版本。a 就是这样来到柯基的。

Corgi 语法的灵感来自于 [Pug](https://pugjs.org/) ，但不是基于 tab 的，所以理论上你可以把一个文件压缩到一行。我喜欢这一点，因为在 Pug 中强制使用装饰性 HTML 标签的 tab 结构真的很笨拙。所以 Corgi 使得 HTML 在结构*和*风格上非常棒。

一个示例性的 Corgi 文档应该是这样的:

```
doctype html
html(
    head(
        title("Corgi Example")
        meta[charset="UTF-8"]
        meta[name="viewport",content="width=device-width,initial-scale=1"]
    )
    body(
        h1("Corgi Example")
        p("This is an example HTML document written in "a[href="https://github.com/corgi-lang/corgi"]("Corgi")".")
        p("Follow Nektro on Twitter @Nektro")
    )
) 
```

Enter fullscreen mode Exit fullscreen mode

## 关闭

制作编译器是困难的，但绝对是有趣的，我希望这有助于揭开他们的神秘面纱。

现在我也有了一个 HTML 前处理器，我将在尽可能多的项目中使用它。

资源:

*   [https://github.com/Nektro/mantle.js](https://github.com/Nektro/mantle.js)
*   [https://github.com/corgi-lang/corgi](https://github.com/corgi-lang/corgi)

跟着我:

*   [https://dev.nektro.net/](https://dev.nektro.net/)
*   [https://twitter.com/](https://twitter.com/)