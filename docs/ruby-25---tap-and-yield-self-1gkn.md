# Ruby 2.5 -轻击并释放自我

> 原文：<https://dev.to/rpalo/ruby-25---tap-and-yield-self-1gkn>

我阅读了[Ruby 2 . 5 . 0 发布简介](https://www.ruby-lang.org/en/news/2017/12/25/ruby-2-5-0-released/)，引用了 [Ben Halpern 的文章](https://dev.to/ben/ruby-ruby-250-was-released-b98)，我发现其中一个新特性是`#yield_self`方法。

```
# This is roughly what it is
class Object
  def yield_self(*args)
    yield(self, *args)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我去做了更多的研究，有人提到和`#tap`很像。“超级！”我想，“……什么是`#tap`？”在做了大量阅读和搜索不同的定义、用例、优点和缺点之后，我最终有了一篇值得研究的博文，所以我想我应该分享我的想法，看看其他人是否有什么要补充的。

为了让你有一个正确的心态，这两种方法都在我称之为“方法流水线”的领域中找到了它们的用例。

## 方法流水作业

我最喜欢 Ruby 的一点(也是我希望 Python 做得和 Ruby 一样好的一点)是，因为一切都是对象，*和*大多数函数是方法，*和*`.`(点)操作符处理方法返回值，你可以随心所欲地在一个“句子”中链接方法。这对于数据操作特别有用，如转换、简化、映射等。这样的代码是可能的:

```
("a".."z")
  .map(&:upcase)
  .map(&:ord)
  .select { |num| (num % 3).zero? && num.even? }
  .sum
# => 390 
```

Enter fullscreen mode Exit fullscreen mode

每一步都将其输出传递给数据管道中的下一步，允许我们用几行代码从范围`"a".."z"`到数字`390`。出于某种原因，这种工作方式真的在我脑海中留下了深刻的印象，我注意到这种风格一直在我的代码中蔓延。`#tap`和`#yield_self`在这个生态系统中也有自己的位置。

## Tap: Unix 的 Tee for Ruby

起初我对`#tap`这个名字感到非常困惑。与`#select, #map, or #reduce`不同的是，`#tap`这个名字最初并没有给我提供任何关于它做什么的线索。经过一番挖掘，这个名字实际上更有意义了。`#tap`是*进入方法管道而不干扰流动的一种方式。你在一个对象上调用`#tap`，它运行你想要的任何代码，并且返回原始对象。因为它返回原始对象，所以您可以将它插入管道中的任何地方，而不会导致任何错误(大多数情况下)。让我们看看上面的例子。假设您需要进行一些调试。* 

```
("a..z")
  .map(&:upcase).tap { |letters| puts "Uppercase: #{letters}"}
  .map(&:ord).tap { |nums| puts "Ascii: #{nums}" }
  .select { |num| (num % 3).zero? && num.even? }.tap { |nums| puts "Filtered: #{nums}" }
  .sum

# Uppercase: Uppercase: ["A", "B", "C", ... "Y", "Z"]
# Ascii: [65, 66, 67, ... 89, 90]
# Filtered: [66, 72, 78, 84, 90]
# => 390 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，添加`#tap`并没有影响流水线中的步骤，它允许我们在每个步骤上做一些好的 ole `puts`调试。

另一个用例是在对象通过管道时对其进行修改——不过，我承认，我很难找到一个现实的理由来解释为什么要这么做。

```
("a".."z")
  .map(&:upcase)
  .map(&:ord)
  .tap { |nums| nums << 6666666 }  # Modifying the array of ASCII ORD numbers in place!
  .select { |num| (num % 3).zero? && num.even? }
  .sum

# => 667056 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下，要点是`#tap`返回它接收到的对象(不管它是否被修改)。

## 屈服自我:拓展你的管道

`#yield_self`有一点不同，因为它返回的是*而不是*阻塞的结果。这里有一个例子说明了它的作用，但是有点没意义。

```
2.yield_self { |num| num * 2 }
# => 4 
```

Enter fullscreen mode Exit fullscreen mode

当我看到这个例子时，我的第一个想法是，“这有什么意义呢？为什么不直接做`2 * 2`？”当你在处理“不可流水线化”的函数时,`#yield_self`真正大放异彩。您可以在两个主要地方找到这些函数:过程和类方法。

```
# Procs/Lambdas must be called in front of their arguments
shout = ->(phrase) { puts phrase.upcase }
puts shout
# => #<Proc:0x00007fcfac82bf80@(irb):1 (lambda)>
# Shout must be used via .call, .(), [], or .===
shout.call("hello, dear friends")
# => "HELLO, DEAR FRIENDS"

# Same with class methods
contents = File.read("test.txt")
# => "Test contents\n" 
```

Enter fullscreen mode Exit fullscreen mode

如果您想将这些整合到某个管道中呢？救援来了！

```
"TXT.TSET"
  .downcase
  .reverse
  .yield_self { |filename| File.read(filename) }
  .yield_self(&shout)
# => "TEST CONTENTS\n" 
```

Enter fullscreen mode Exit fullscreen mode

*&大声感谢[乔尔·拉尔森](https://dev.to/joellarsson)帮我修复了上面例子中的 proc 调用！*

这使你不必做两个步骤。

```
contents = File.read("TXT.TSET".downcase.reverse)
shouty_results = shout.(contents) 
```

Enter fullscreen mode Exit fullscreen mode

它还使您的代码看起来垂直和管道 y，而不是水平嵌套。此外，想象一下，如果您有几个想要使用的类方法或过程！嵌套函数很快变得难以阅读。

## 总结起来

这是我从研究中得到的。是全新的，所以我相信随着我们更多地使用它，我们会想出使用它的“正确”和“错误”的方法。在做研究的时候，我不得不费力地阅读数十亿条评论，比如“yield_self 很蠢”、“我讨厌这个名字”和“F#更好”，所以我非常明确地说，我不想再看这些了，但是如果你有关于`#tap`或`#yield_self`的其他用例或想法，我很想听听它们。

## 其他资源

*   [波格丹一世·登科维奇的博客文章](https://bogdanvlviv.github.io/posts/ruby/new-method-kernel-yield_self-since-ruby-2_5_0.html)
*   [迈克尔·拉姆斯基的博客帖子](https://mlomnicki.com/yield-self-in-ruby-25/)
*   [这个 StackOverflow 回答](https://stackoverflow.com/a/47890832/4100442)
*   [奥古斯特的这篇文章](http://augustl.com/blog/2008/procs_blocks_and_anonymous_functions/)

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2017/12/29/tap-and-yield-self/)*

*还有，基于 Dev.to 的服务器时间，今天是我的生日！生日贴！*

2018 年 4 月 8 日更新:修正了一个例子中的 proc 调用。再次感谢，[乔尔](https://dev.to/joellarsson)！*