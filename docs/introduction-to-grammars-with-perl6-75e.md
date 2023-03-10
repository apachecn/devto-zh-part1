# 樱庭落语法导论

> 原文：<https://dev.to/jj/introduction-to-grammars-with-perl6-75e>

[![There's a bit of text here if only I knew how to extract it](img/5557dce4ec52e70585d45037fbd3b211.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JVh5MR_s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9aig11bp90c3lwk6721g.jpg) 
一般来说，处理一个文本涉及到很多[正则表达式](https://dev.to/hawkinjs/dont-fear-the-regex-a-practical-introduction-to-regular-expressions)加上`splits`加上一般的骂骂咧咧，气呼呼，直喘粗气。语法解决了这个问题。他们一边接收文本，另一边输出数据。它们被称为(并将再次被称为)*类固醇正则表达式*，但是正则表达式专注于提取和理解文本的部分，而语法专注于分析整个结构，理解语法以及文本的不同部分如何相互关联。显然，Perl6 是唯一一种将语法作为基本特性的语言，它从一开始就这样做了。这就是为什么[我要用它来写这篇文章](https://perl6.org/)。

> 另外，因为[在 dev.to](https://dev.to/search?q=perl6) 中似乎没有太多。所以我也可以从这个开始。

因此，假设我们想要一个增强的文本的纳米降价语法；它基本上是文本，可能有也可能没有围绕特定单词的`*`用于增强。所以我们想处理像这样的段落:

```
"This includes one *enhanced* word" 
```

Enter fullscreen mode Exit fullscreen mode

我们想要从中收集的结构实际上是可能会或可能不会被增强的单词。稍后我们可以用它们做一些事情，比如转换成 HTML，但是现在我们想要的是基本的结构。我们可以在 Perl6 中这样表达:

```
grammar Enhanced-Paragraph {
    token TOP { <superword>[ (\s+) <superword>]+ }
    token superword { <word> | <enhanced-word> }
    token word { \w+ }
    token enhanced-word { \* <word> \* }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们称之为`grammar`，Perl6 允许标识符中间有破折号。所以我们习惯称之为`Enhanced-Paragraph`。我们需要顶部的`TOP`规则，它说这种段落至少包括一个`superword`，后跟空格(`\s+`，从正则表达式中提取语法)和一个或多个超级单词；方括号后面的`+`就是这个意思。*令牌*将在后面定义，出现在`<>`之间，整个事情类似于被`{}`包围的代码块。我们需要定义`tokens`，它是语法的组成部分。还有规则和正则表达式，但就目前而言，这已经足够了。
规则`superword`表明一个段落可以包含普通的`words`或者`|`的意思是`enhanced-word`。同样，我们可以用破折号来命名令牌，没问题。

> 事实上，Perl6 可以使用所有种类的*字母* unicode 符号。稍后会有更多的介绍。

我们深入到最深层，定义`word`，我们必须在这里使用一个正则表达式，`\w+`表示一个或多个字母数字符号。`0`是一个词，还有`why`或者`þor`，但不是`----`。而一个`enhanced-word`就像一个单词，只是被`*`包围着，我们必须用`\`对其进行转义，因为它们在语法中有意义，一个事物的 0 次或更多次重复。空白在这里并不重要，它只是用来让一切更容易理解。

这就是了。我们可以马上这样使用:

```
grammar Enhanced-Paragraph {
    token TOP { <superword>[ (\s+) <superword>]+ }
    token superword { <word> | <enhanced-word> }
    token word { \w+ }
    token enhanced-word { \* <word> \* }
}

my $paragraph = "þor is *mighty*";
my $parsed = Enhanced-Paragraph.parse($paragraph);
say $parsed; 
```

Enter fullscreen mode Exit fullscreen mode

会说:

```
｢þor is *mighty*｣
 superword => ｢þor｣
  word => ｢þor｣
 0 => ｢ ｣
 superword => ｢is｣
  word => ｢is｣
 0 => ｢ ｣
 superword => ｢*mighty*｣
  enhanced-word => ｢*mighty*｣
   word => ｢mighty｣ 
```

Enter fullscreen mode Exit fullscreen mode

给你结构的内幕:三个组成部分，其中一个恰好是一个加强词。Perl6 使用*符号*表示变量，`$`用于任何类型的变量；还有`my`，它实际上是一个作用域声明，并使它成为当前作用域内的一个词法变量。意思:不太在乎类型，但就用在这里。
我们使用方法`parse`使用语法，就像它们是对象一样:语法解析它遇到的任何东西，如果它理解它，就创建一个好的数据结构，我们将它存储在变量`$parsed`中。我们可以打印变量，默认情况下，Perl6 为我们创建了一个很好数据结构，就像我们看到的那样。该数据结构包括输入`｢þor is *mighty*｣`，它使用了 Perl6 特有的引用结构，然后是一个键-值对数组，它包含作为键的结构类型(如`superword`)和作为值的结构类型，同样使用方括号。

我们可以更进一步，把它变成一个可执行文件:

```
#!/usr/bin/env perl6

grammar Enhanced-Paragraph {
    token TOP { <superword>[ (\s+) <superword>]+ }
    token superword { <word> | <enhanced-word> }
    token word { \w+ }
    token enhanced-word { \* <word> \* }
}

sub MAIN ( Str $paragraph ) {
    my $parsed = Enhanced-Paragraph.parse($paragraph);
    say $parsed;
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Perl6 中,`sub MAIN`声明了一些可以运行的东西，这并不是严格需要的，但是如果你想有一些特定类型的变量，比如本例中的`Str`或 string，我们把它赋给了`$paragraph`，这就很好了。这个函数将析构参数，在这种情况下很简单，并将它们放入变量中供我们使用。
它可以在`chmod +x`之后运行，或者在你最喜欢的操作系统
中使用任何咒语

```
$ ./mini-grammar.p6 "We *want* *cookies*"
｢We *want* *cookies*｣
 superword => ｢We｣
  word => ｢We｣
 0 => ｢ ｣
 superword => ｢*want*｣
  enhanced-word => ｢*want*｣
   word => ｢want｣
 0 => ｢ ｣
 superword => ｢*cookies*｣
  enhanced-word => ｢*cookies*｣
   word => ｢cookies｣ 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们除了打印结果结构之外什么也没做，但我打算从 post 开始的这篇简短但实际上更长的文章的目标是展示语法如何将无意义的文本转化为结构。稍后会有更多的介绍。也许吧。

## 鸣谢

非常感谢[莫里茨](https://github.com/moritz)，他向我解释了动作是如何工作的，[在这里修复了我的代码](https://gist.github.com/moritz/7406bb757d01269345cc9eb4d6bd3dbc)

## 暂时不要离开

继续阅读本系列的下一篇文章[用 Perl 6 语法匹配事物](https://dev.to/jj/matching-things-with-perl-6-grammars-ao9)