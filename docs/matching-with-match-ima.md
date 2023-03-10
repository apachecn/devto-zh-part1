# 以赛配赛。

> 原文：<https://dev.to/jj/matching-with-match-ima>

[![Scroll in a brick wall](img/306f488d7e05e7c36cf3275919d85581.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5uJC2k7X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yag8k5d3ejaszgdj9mt5.jpg)

语法和解析是关于从文本中提取结构。但是一旦你有了这个结构，就必须有一种方法来真正地表达和使用它，这样无论机器在文本中搜寻什么，都可以得到很好的利用。

许多语言对此并不大惊小怪。你想要这样或那样的文本，这是你的文本。例如，Python

```
match = re.findall("\*(\w+)\*", sys.argv[1]) 
```

Enter fullscreen mode Exit fullscreen mode

这将再次查找两个星号内的所有单词，或者更确切地说是字母数字组合。这将这样做:

```
other/match.py "this *is* it" 
['is'] 
```

Enter fullscreen mode Exit fullscreen mode

也就是说，一个包含所有匹配了*正则表达式*的单词的数组。还有:

```
$ other/match.py "this *could* be *it*"
['could', 'it'] 
```

Enter fullscreen mode Exit fullscreen mode

两个词符合描述，这是你的词，先生或女士。然而，这些话从何说起呢？如果你想以某种方式标记出你想要的结构在哪里，你将不得不返回并使用任何函数在另一个中搜索一个字符串。

## 肯定有更好的办法做吧？

进入[的`Match`对象](https://docs.python.org/3/library/re.html#match-objects)。这个对象由`re.search`和`re.match`返回，就像`search`，只是它只匹配字符串开头的

 *> 对于包含这类东西的正则表达式语言，我真的不明白为什么你需要这两种方法。总之。

```
match = re.search("\*(\w+)\*", sys.argv[1]) 
```

Enter fullscreen mode Exit fullscreen mode

应用到上面同样的事情，会返回`<_sre.SRE_Match object; span=(5, 12), match='*could*'>`(使用`pprint`)，实际上也可以访问被捕获结构的*组*；在这种情况下，`match.group(1)`将返回`could`。

但是，其余的匹配字符串会怎么样呢？嗯，你*可以*这样做:

```
this_string = sys.argv[1]
while match is not None:
    match = re.search("\*(\w+)\*", this_string )
    pp.pprint( match )
    if match is not None:
        print( match.group(1) )
        this_string = this_string[match.start(1):] 
```

Enter fullscreen mode Exit fullscreen mode

因为`search`只找到第一个，你可以回到`findall`然后寻找字符串...

## 或者你可以试试 Perl6

```
my @matches = @*ARGS.map( {$^þ ~~ m:g/\* ~ \* (\w+)/} );

say @matches.map: { $^þ.perl ~ "\n" }; 
```

Enter fullscreen mode Exit fullscreen mode

是的，我知道，有点吓人，对吧。而是*两条线*。甚至还有一个:

```
say @*ARGS.map( {$^þ ~~ m:g/\* ~ \* (\w+)/} ).map: { $^þ.perl ~ "\n" }; 
```

Enter fullscreen mode Exit fullscreen mode

从命令行:

```
perl6 -e 'say @*ARGS.map( {$^þ ~~ m:g/\* ~ \* (\w+)/} ).map: { $^þ.perl ~ "\n" };' "It *should* match *all* the *stuff*" 
```

Enter fullscreen mode Exit fullscreen mode

是的，很长的队伍。但只有一个。

无论如何，这里有一点看起来可怕的 Perl6，一旦你掌握了它的要点，它实际上并不是什么大不了的事情。首先，`@*ARGS`是一个数组(因此开头是`@`),它包含通过命令行给脚本的参数。这不仅适用于第一个字符串，实际上也适用于任何一个字符串。

Perl6 也非常[功能](https://dev.to/robsinkko/learning-functional-programming-go)。在有循环的地方，应该有一个`map`，*通过应用一个操作将一个数组的每个元素映射*到其他东西。哪个行动？括号里的那个:

```
{$^þ ~~ m:g/\* ~ \* (\w+)/} 
```

Enter fullscreen mode Exit fullscreen mode

所谓的 *twigil* `$^þ`使用的是冰岛语字母 thorn，我喜欢并且应该到处使用*只是一个占位符，它依次接受数组中每个成员的值；在这种情况下，它代替了一个元素，这个元素与右边的正则表达式匹配(因此是 Perl6 中的`~~`、[匹配操作符)。你可能已经看过](https://docs.perl6.org/language/regexes)[语法文章](https://dev.to/jj/matching-things-with-perl-6-grammars-ao9)，所以这可能不会让你感到害怕，但是让我们来解构一下:* 

```
m:g/\* ~ \* (\w+)/ 
```

Enter fullscreen mode Exit fullscreen mode

`m`表示后面是将要匹配的正则表达式。这里还有其他的 *ops* ，但是我们暂时不去那里。`:g`是一个副词*g *全局化搜索，这意味着我们将不必像以前那样通过(h|l)oops 来获得所有匹配。这将匹配*一个句子中的所有*单词，而不是第一个。

Perls 使用`//`作为正则表达式的引用结构，这也无妨。它们与其他琴弦有着明显的区别。我们已经到了正则表达式本身，就此而言，它与 Perl5 和任何其他语言都不一样

> 实际上，*所有的*语言都是从 Perl 复制正则表达式的。但那是后话了。

`~`是一个*匹配对*指示器。它会说:“我希望我的东西被匹配的对或字符包围”。例如，`( ~ )`将匹配 parenteses，在我们的例子中，`\* ~ \*`将匹配一对星号(被转义，因为它们实际上在 regex 上下文中有意义)。但是实际上什么是两个里面的任何东西都必须遵循的表达式呢？它就在后面，`(\w+)`带括号，实际上意味着我们将*捕获*只是表达式的那一部分。总而言之，我们的意思是“给我一对星号中间的字母数字字符”。

一旦你掌握了，就没那么难了，对吗？

也很强大。`~~`将实际返回一个 [Perl6 `Match`](https://docs.perl6.org/type/Match) ，这实际上是我们(不那么漂亮)使用`.perl`打印的，它返回一个可以计算为 Perl6 对象的表达式。有点像 JSON，但是对于 Perl。可以说是 PSON。看起来像这样的

```
($(Match.new(list => (Match.new(list => (), made => Any, pos => 10, hash => Map.new(()), orig => "It *should* match *all* the *stuff*", from => 4),), made => Any, pos => 11, hash => Map.new(()), orig => "It *should* match *all* the *stuff*", from => 3), Match.new(list => (Match.new(list => (), made => Any, pos => 22, hash => Map.new(()), orig => "It *should* match *all* the *stuff*", from => 19),), made => Any, pos => 23, hash => Map.new(()), orig => "It *should* match *all* the *stuff*", from => 18), Match.new(list => (Match.new(list => (), made => Any, pos => 34, hash => Map.new(()), orig => "It *should* match *all* the *stuff*", from => 29),), made => Any, pos => 35, hash => Map.new(()), orig => "It *should* match *all* the *stuff*", from => 28))
) 
```

Enter fullscreen mode Exit fullscreen mode

又一次，看起来很吓人。但是我们在这里看到了一些东西。这是一个嵌套结构，匹配包含匹配。我们可以在这里做非常复杂的事情。我们还有`pos`，它们是匹配的位置，`orig`，它们是被匹配的位置，还有`from`。但是我们也有一个强大的数据结构，我们可以从中提取匹配的字符串和我们捕获的内容:

```
perl6 -e 'say @*ARGS.map( {$^þ ~~ m:g/\* ~ \* (\w+)/} ).map: { $^þ.list ~ "\n" };' "It *should* match *all* the *stuff*"
(*should* *all* *stuff*
) 
```

Enter fullscreen mode Exit fullscreen mode

使用`list`。如果我们知道只有一个参数，我们就把它简化为

```
perl6 -e 'say (@*ARGS[0] ~~ m:g/\* ~ \* (\w+)/).map( {$^þ.caps} );' "It *should* match *all* the *stuff*"
((0 => ｢should｣) (0 => ｢all｣) (0 => ｢stuff｣)) 
```

Enter fullscreen mode Exit fullscreen mode

`caps`返回被匹配成对的*索引*，值。这里我们有 3 个`Match`对象，我们可以用许多不同的方式来使用它们来呈现或处理将要提取的信息。

## 并将其提取出来

我们已经在这个系列的前一篇文章中[做过了。不知不觉中，我们一直在来回拖动`Match`物体，我们实际上已经看到了一个。语法和正则表达式一样，也返回`Match`对象。但是我们也可以在语法中使用它们*。这也是我们在不久的将来要做的。*](https://docs.perl6.org/type/Match#method_list)**