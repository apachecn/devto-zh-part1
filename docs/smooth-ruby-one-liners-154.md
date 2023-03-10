# 光滑的红宝石一行程序

> 原文：<https://dev.to/rpalo/smooth-ruby-one-liners-154>

当我刚开始学习 Ruby 的时候，我读的第一本书前面有一章是关于所有的命令行选项和`ruby`命令的标志。像往常一样，我没有看太多，只是快速浏览了一下那一部分。“是啊，是啊，是啊，”我对自己说，我拉开了我的编辑器。“随便吧。去找好东西吧。给我看看那些*物体*！那些*类！*“好吧，我在这里说:我错过了。我现在正在读的另一本书的一章([Andre Ben-Hamou 的《系统管理实用 Ruby》](http://www.apress.com/us/book/9781590598214))也有这方面的内容，这一次我注意到了。现在我想和大家分享我的亮点！

## 好东西

### 简单执行

让我们从简单的开始。`-e`标志将让 Ruby 解释器内联执行一个命令字符串，并将结果输出到`stdout`。如果你不记得那个该死的 Bash 命令来做浮点运算的话，这很有用(*提示:是`bc`* )！

```
$ ruby -e 'puts (Math.sqrt(32**2/57.2))' > calc.txt 
```

Enter fullscreen mode Exit fullscreen mode

关于引号的一个注记。许多有用的 Ruby 一行程序都包含像`$_, $<, and $.`这样的特殊变量。这一切都很好，直到您将 Bash 字符串插值添加到组合中。因为您在 Bash 中插入了这样的变量:

```
echo "That's no $silly_thing, that's my wife!" 
```

Enter fullscreen mode Exit fullscreen mode

如果您用双引号将 Ruby 命令括起来，那么您的特殊变量将会产生不可预知的结果。最好始终用单引号将命令括起来。

如果您顽固地拒绝 Google Bash 命令，第一个例子会有点用，但只会稍微方便一点。但请不要离开我，因为我们才刚开始热身。

### 逐行处理

`-n`标志将您执行的一行程序包装在一个隐式的`while gets ... end`块中。当您将这一点与 Ruby 的特殊全局变量`$_`结合使用时，它存储了最近的`Kernel.gets`命令的结果，您可以做一些漂亮的(**和可读的**)文件处理！

```
$ ruby -ne 'puts $_.strip if $_ =~ /soup/' /home/rpalo/recipes 
```

Enter fullscreen mode Exit fullscreen mode

它将去掉多余的空格，并打印出包含单词“soup”的每一行。我不知道你怎么想，但这样的脚本对我的基础设施至关重要。

如果过一段时间你厌倦了敲`puts $_.something.something`的话，不用担心。`-p`标志在命令的末尾删除了一个隐式的`print $_`，因此您可以修改变量。我们可以这样清理前面的例子:

```
$ ruby -pe '$_.strip! if $_ =~ /soup/' /home/rpalo/recipes 
```

Enter fullscreen mode Exit fullscreen mode

*Edit 2/19/18:* 事实上，`$_`变量是默认变量，它会根据任何正则表达式进行测试，所以上面的行可以简化为:

```
$ ruby -pe '$_.strip! if /soup/' /home/rpalo/recipes 
```

Enter fullscreen mode Exit fullscreen mode

你至少保存了 4 个完整的字符。不要把钱都花在一个地方。

### 就地文件编辑

“我觉得我不是一个真正的开发人员，因为我没有花时间学习使用`sed`或`awk`命令！”首先，这是愚蠢的。你应该忽略那些以“如果你不是一个真正的开发人员”这样的句子开头的人。另外，当你就地编辑文件时，你可以让他们感到难过(就像`sed -i`做的那样)！这里有四种方法可以做到这一点，按照湿滑程度的顺序排列。假设您想从一个配置文件中删除默认注释(不推荐，除非您承诺添加您自己的注释)。

*2018 年 2 月 19 日编辑:将输入和输出重定向到同一个文件是不好的(清除整个文件)。下一个片段已经更新，以反映这一点。更多细节见评论。另外，`gsub`遵循与正则表达式相同的规则，默认情况下对`$_`进行操作，因此可以省略。*

```
$ ruby -pe 'gsub(/#.*$/, "")' .myconfig > .myconfig.new
$ mv .myconfig .myconfig.old
$ mv .myconfig.new .myconfig 
```

Enter fullscreen mode Exit fullscreen mode

将修改的行重定向到一个新文件，而不是打印到`STDOUT`。如果使用`-i`标志，您可以省去所有额外的工作。

```
$ ruby -i -pe 'gsub(/#.*$/, "")' .myconfig 
```

Enter fullscreen mode Exit fullscreen mode

当您想要对多个文件运行该命令时，这非常有用。

```
$ ruby -i -pe 'gsub(/#.*$/, "")' *.conf 
```

Enter fullscreen mode Exit fullscreen mode

glob match 将遍历每个以。确认并覆盖它们。

最后，如果你是个偏执狂和规避风险的人(也就是说，你使用终端的时间已经够长了，知道用你的脚本中的错误覆盖一个文件并失去一切的痛苦- #GodBlessGit)，你可以像这样给你的`-i`标志添加一个文件扩展名:`-i.bak`，它会在写之前用那个文件扩展名保存旧版本；`.myconfig`不会有评论，但会有一个新的`.myconfig.bak`仍然有。

> 在`-i`和备份文件扩展名之间不能有空格，否则将不起作用。你已经被警告了。

### 耕种一行行田地

我想说两个比较特殊的全局变量:`$< and $.`。第一个引用输入的文件。第二个引用当前的行号。(注意这意味着`$. and $<.lineno`是同义的)。你可以像这样实现一个粗略版本的`head`:

```
$ ruby -ne 'puts $_ if $. <= 10' test.txt 
```

Enter fullscreen mode Exit fullscreen mode

要使用可能被分隔的文件中的字段，比如 csv 或类似于`/etc/passwd`的系统文件，您可能首先尝试这样做:

```
$ ruby -ne 'puts $_.split.first + $_.split.last' test.txt 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是您可以使用代表“自动拆分”的`-a`来完成繁重的工作。它将预分割字段放入一个名为`$F`的特殊变量中。

```
$ ruby -a -ne 'puts $F.first + $F.last' test.txt 
```

Enter fullscreen mode Exit fullscreen mode

不过，上面的例子只使用空格作为分隔符！可以用`-F`指定*那个*。

```
$ ruby -a -F: -ne 'puts $F.first' /etc/passwd 
```

Enter fullscreen mode Exit fullscreen mode

### 安装和拆卸

如果你*用过*`awk`，那么这下一部分可能很熟悉。使用`BEGIN`和`END`模块，你可以在运行输入文件之前做一些初始化工作，然后拆卸/最终输出。对于计算匹配某个模式的行的总数这样的事情很有用。

```
$ ruby -ne 'BEGIN { ducks = 0 }; ducks += 1 if $_ =~ /ducks/; END { puts ducks }' duckfile.txt 
```

Enter fullscreen mode Exit fullscreen mode

使用这种设置和拆卸，您的一行程序会变得更加强大。

### 需要模块

您可能已经在其他方面使用了它，但是您可以使用`-r`命令从标准库中获取 gem 和模块。例如，要列出您机器的 IP 地址，您可以这样做:

```
$ ruby -rsocket -e 'puts Socket.ip_address_list.map(&:inspect)' 
```

Enter fullscreen mode Exit fullscreen mode

玩质数！

```
$ ruby -rprime -e 'puts Prime.first(20)' 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

总的来说，这些可能不会给你 10 倍的生产力提升，但它们很有趣，我觉得它们提供了一种有趣的方法。如果你已经深入到一个 Ruby 项目中，并且把你的大脑切换到 Bash 上需要做太多的工作，那么它们看起来特别有用。这篇文章写到一半的时候，我去了谷歌，关于这个话题的博客文章数量惊人。(说实话，发布这个消息让我觉得自己有点站不住脚)。但这是一个有趣的话题，也是让 Ruby 使用起来更有趣的原因之一，所以我觉得分享一下会很好。不管怎样，如果你有任何你一直在使用的很酷的 Ruby 命令行程序，请发 tweet/message 给我！我会把它们添加到这个列表中:

## 由牛人提交

```
## First two submitted by me to make this not be a sad empty list :|
# Delete trailing whitespace
$ ruby -pe 'gsub(/\s+$/, "\n")'

# Prints too long lines
$ ruby -ne 'puts $_ if $_.length > 80' 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*2018 年 2 月 19 日编辑感谢 [@learnbyexample](https://dev.to/learn_byexample) 的智慧。*

*原帖 [`assert_not magic?`](https://assertnotmagic.com)*