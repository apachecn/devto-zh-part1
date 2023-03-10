# 耦合、配对和测试

> 原文：<https://dev.to/jj/coupling-and-pairing-and-testing-10ee>

[![Caught inside a pair](img/bfaa127a383ef57758f1025e9d73e1e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Idby3cCN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i0l4ceat9io6va29gn1j.jpg)

在[理解了多重分派](https://dev.to/jj/multiple-dispatch-37d3)和[学习了角色](https://dev.to/jj/role-ing-on-the-grammars-1bd5)之后，我们有点想把所有的东西放在一个单一的东西里，来解析一些有一定分量的东西，比如一个段落。让我们用这个 :
试试

```
grammar paragraph  {
    token TOP { <chunk>[ (\s+) <chunk>]* }

    regex like-a-word { «\H+» }

    regex span { <like-a-word>[(\s+) <like-a-word>]* }

    proto regex quoted {*}
    regex quoted:sym<em> { ('*') ~ '*' <span> }
    regex quoted:sym<alsoem> { ('~') ~ '~' <span> }
    regex quoted:sym<code> { ('`') ~ '`' <span> }
    regex quoted:sym<strong> { ('**') ~ '**' <span> }
    regex quoted:sym<strike> { ('~~') ~ '~~' <span> }

    regex link { '[' ~ ']' <span> '(' ~ ')' (\H+) }

    regex chunk {  <quoted> | <span> | <link> }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您一直在关注这个系列，那么没有什么是真正新的。我们把所有的东西放在一个单一的语法中，如果你从`TOP`开始往下看，就会从`chunks`中产生一个段落，它可以是一个链接，也可以是我们之前见过的引用/未引用的单词序列。常用的降价链接结构`link`使用了两次配对操作符`~`；`~`是内部内容的占位符，一个`span`和一组字符，是
*而不是*水平空格`\H+`。这也是用括号捕获的，这样我们以后就可以使用它了。

## 我们来测试一下

语法是类，是代码，代码要测试；如果不是，那就是坏了。到目前为止，我们已经使用了一些老式的调试方法，但是我们真的必须检查我们想要发生的事情是否真的发生了。让我们使用 Perl 6 中最初命名的`Test`模块进行测试，我们将它`use`到我们的程序中。

```
my $simple-thing = paragraph.parse("Simple **thing**");
isa-ok( $simple-thing, "Match", "Is a Match");
can-ok( $simple-thing<chunk>, "list", "Can do lists");
is( $simple-thing<chunk>.elems, 2, "Two chunks");

my $not-so-simple-paragraph= paragraph.parse("This is *a simple* _paragraph_ with ~~struck~~ words and [links](https://to.nowhere)");
is( $not-so-simple-paragraph<chunk>.elems, 6, "6 chunks");
like( ~$not-so-simple-paragraph<chunk>[0], /This/, "Chunking OK");
is( $not-so-simple-paragraph<chunk>[5]<link><span>, "links", "Links");

my $period = "This ends with a period.";
like( ~$period, /\./, "Symbols are good");

done-testing(); 
```

Enter fullscreen mode Exit fullscreen mode

在将解析结果赋给一个变量之后，我们在这里使用了几个测试函数。`isa-ok`检查类别或类型。返回的是一个`Match`吗？这就是[语法返回的内容，正如我们在](https://dev.to/jj/matching-with-match-ima)之前看到的。语法将不返回任何东西，如果不返回，这种虚无将具有类型`Any`。
这还不够。一个段落就是一个组块列表，那么`$simple-thing<chunk>`有那个能力吗？`can-ok`检查一下。这个简单的东西应该只有两个组块，一个`span`和一个`quoted`。检查实际上有两个，而且只有两个块。

如果事情变得复杂，我们将不得不以不同的方式测试它。分块正确吗？时间段`.`实际上包含在块中吗？我们用`~`字符串化结果，它在这里有不同的作用

```
like( ~$period, /\./, "Symbols are good"); 
```

Enter fullscreen mode Exit fullscreen mode

并检查类词提取部分是否考虑了像句号这样的非真正的词。

## 做过测试

我们以`done-testing()`结束

我们应该把这个放到另一个文件里。测试是一件严肃的事情，它确实遵循一些协议，比如在 Perl 和 Perl6 中总是使用相同的目录。

完成了吗？差不多是这样。基本上我们想要的都已经测试过了。当然，我们应该添加[覆盖测试](https://en.wikipedia.org/wiki/Code_coverage)，但是当我写这篇文章的时候，似乎没有针对 Perl6 语法的东西。我们必须等到明年圣诞节，那时 Perl6 中的一切都会发生。