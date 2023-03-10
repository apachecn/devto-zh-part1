# 承担角色

> 原文：<https://dev.to/jj/assuming-roles-2dog>

[![Mariano does Chavo del Ocho, graffiti in Granada](img/6b1319e447b7a7039e9eae6316c3dfc2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iIk3Q1hf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ujoi0wca6qamrrkmmlx4.jpg) 
我见过很多谈论面向对象的文本使用本体隐喻。兰博基尼是一辆汽车，也是一种交通工具。作为隐喻来说还不错，但是它把你固定在一个层次结构中，在那里你不断完善直到你得到你想要的，如果你想混合一些东西，比如说一辆会飞的汽车，你有时不得不以一种对任何人都不愉快的方式把两棵树嫁接在一起。
幸运的是，现代的面向对象编程，在遵循通用结构的同时，也允许更现代的、基于组件的类。这些组件通常被称为 [mixins](https://en.wikipedia.org/wiki/Mixin) ，也可以是角色、模块、接口或特征。想要一辆会飞的汽车？抓住一个 Trabbi，鸭磁带一些风筝，在这里你去！

## 许多现代语言都包含这些 mixins。

例如，Ruby 称它们为[模块](http://ruby-doc.com/docs/ProgrammingRuby/html/tut_modules.html)。我们可能希望，正如[我们](https://dev.to/jj/matching-things-with-perl-6-grammars-ao9) [已经](https://dev.to/jj/introduction-to-grammars-with-perl6-75e) [完成](https://dev.to/jj/matching-with-match-ima)一样，用这些类来描述文本

```
#!/usr/bin/env ruby

module Words
  attr :words
  def to_string( ligature ) 
    return @words.join(ligature)
  end
end

class Paragraph
  include Words

  def initialize(words)
    @words = words
  end

  def to_string()
    return "\n"+@words.join(" ")+"\n"
  end
end

sentence = Paragraph.new("these are the words".split(/\s+/))
puts sentence.to_string() 
```

Enter fullscreen mode Exit fullscreen mode

文本事物包含单词，所以我们定义`Words`来包含它们。我们将它定义为`module`,以表明这不是一个完整的东西，而只是一个组件，就像乐高一样，一旦它被包含在一个成熟的`Class`中，就开始有意义，就像下面的这个，它在开始时做`include Words`,然后直接使用组件定义的变量`@words`来初始化它并打印东西。真的不是开创性的东西，在这种情况下，除此之外，也许可以通过继承来使用，但实际上是一个`Paragraph` `is-not-a` `Words`，所以我们在这里是本体安全的，除此之外，我们还创建了一个可以独立测试和管理的简单组件。

## 而 Perl6 也包含 mixins。

只是它称他们为[角色](https://perl6advent.wordpress.com/2009/12/18/day-18-roles/)，但是他们几乎是一样的。让我们看看这个东西是如何工作的:

```
#!/usr/bin/env perl6

# Thanks to https://perl6advent.wordpress.com/2009/12/18/day-18-roles/
role Words {
    has @.words;
    method to-string( Str $ligature ) {
    return @!words.join( $ligature );
    }
}

class Paragraph does Words {

    submethod BUILD( :@!words ) {
    }

    method to-string() {
    return "\n" ~ self.Words::to-string( " " ) ~ "\n";
    }
}

my $sentence = Paragraph.new(words => "these are the words".split(/\s+/));
say $sentence.to-string(); 
```

Enter fullscreen mode Exit fullscreen mode

除了我们需要显式声明`@.words`，在这种情况下是作为一个公共变量，可以简单地使用`words`从对象中自动检索，我知道，它与 Ruby 的非常相似，甚至到了`@`，顺便说一下，这是 Ruby 从 Perl5 中获得的。Perl6 使用关键字`has`来声明变量，因为在*中这个角色有单词*。Perl6，可以大声朗读的语言。与 Ruby 不同，用于声明的 twigil(`@.`)在您实际在对象内部使用时变成了`@!`。为什么？嗯，这一代人有点自我关注，以至于在对待自己的时候会发出感叹。Self → `!`,前面的`@`表示我们正在处理一些类似数组的东西。我们还使用`Str ligature`来声明`to-string`方法的签名；如果我们以其他方式使用它，它会出错，而且它也应该出错，因为非字符串数据没有什么意义。

但是角色是做出来玩的，`Paragraph`做`Words`。然而，这是一个类，我们必须在构造函数中给实例变量赋值，这是 Perl6 中的一个`submethod`。子方法不能被继承，你不能真正继承这个语言中叫做`BUILD`的构造函数，对吗？每一个物体都有它自己的一点点方式来引导自己存在；然而，因为这一段确实是单词，仅仅提到我们将要初始化的变量就足以生成赋值代码。`:@!words`是*的简写，我们将使用散列*(因此是`:`)和键`words`来初始化`@!words`变量。这是我们稍后要做的:

```
my $sentence = Paragraph.new(words => "these are the words".split(/\s+/)); 
```

Enter fullscreen mode Exit fullscreen mode

`splits`使用正则表达式进行拆分

> 我们在同一个“语法是我的朋友”系列的[这篇文章](https://dev.to/jj/matching-with-match-ima)中简要地看到了这一点。

给出一个单词数组，这是`@!words`真正想要的。但是我们需要使用`to-string`方法，它的名称与`Role`中的方法完全相同。毫不费力:

```
return "\n" ~ self.Words::to-string( " " ) ~ "\n"; 
```

Enter fullscreen mode Exit fullscreen mode

`self.Words`将提取段落中的*冗长的*部分，并使用那个特定的`to-string`，而不是这里的那个，引发一个将吞噬整个宇宙的无休止的循环。`~`在这种情况下串联起来

> 也是纤维质的。我们将在本系列的稍后部分回到这个角色。

我们可以为我们的角色
定义一个不同的*客户端*

```
class Heading does Words {

    submethod BUILD( :@!words ) {
    }

    method to-string() {
    return "\n<h1>" ~ self.Words::to-string( " " ) ~ "</h1>\n";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这几乎是一样的，除了它返回一个一级 HTML 标题。

## 我们可以用这个做很多事情

但是我们在谈论语法。[perl 6 中的语法实际上是类](https://www.reddit.com/r/perl6/comments/7ezrz7/ive_written_a_small_bbc_news_scraper_in_perl_6/dqfegsr/)。

所以我们对他们了解得越多越好。我们很快就会愉快地回到语法上来。