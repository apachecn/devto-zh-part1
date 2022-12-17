# 秘密消息

> 原文：<https://dev.to/rpalo/secret-message-7do>

今天我在 Dev.to 上读了一篇 Ben Greenberg 写的关于面试编码挑战的文章，我被吸引住了，不得不自己尝试一下。我强烈建议你在阅读这篇文章之前先看看[的原帖](https://dev.to/benhayehudi/solving-a-job-application-code-challenge-30d)，这样你会有一些背景知识。也就是说，对于懒惰的人，让我给你...

## 一些背景

挑战是这样的(复制自原帖):

> 对以下字符串中的字符进行排序:
> `abcdefghijklmnopqrstuvwxyz_`
> 
> 按字符在以下文本中出现的次数(降序):
> 
> …[为简洁起见，省略了字符串]
> 
> 现在获取排序后的字符串，并删除所有在 _ 后面的字符。剩下的一个字就是答案。

如果你想亲自尝试，你可以在他的帖子的评论部分(我向ðÿ˜要的地方)找到这个超长的字符串。你应该！并分享你的解决方案！不管怎样，继续这个故事。

## 我的解决方案(上)

如果这还不够清楚，我将分享我的解决方案。如果你想在没有剧透的情况下自己解决这个问题，那就继续下去，直到你完成为止。在你写完之前，我不会再写了。ðŸ˜„

[![Waiting...](img/1d2b781bd3c6c5cad389caa16a14ce8b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GOWcsVLn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/slo3qoinf0ybrdmyjvat.gif)

完了？太好了！向前。

我决定使用 Ruby，因为 Ruby 既有趣又棒。最初，我只是想敲出我能想到的第一个解决方案——不管它有多低效或缓慢——然后回来清理它。

```
# I stored the huge string in a separate file called
# `blob.txt` so as to not clutter my main script

blob = File.read('blob.txt')
letters = 'abcdefghijklmnopqrstuvwxyz_'.chars

def naive_message(letters, blob)
  letters
    .sort_by { |letter| blob.count(letter) }
    .reverse  # to get desc. order
    .join
    .split('_')
    .first
end

puts naive_message(letters, blob) 
```

Enter fullscreen mode Exit fullscreen mode

我不会破坏这个答案，但我们只能说，我“祈祷，恳求，恳求，敦促”你自己尝试一下。可能是过去式。我认为初始文本 blob 的 markdown 格式可能扭曲了“_”字符的数量，导致我的答案末尾的字符比实际应该有的略多。更新:完全是这么回事。[精确的文本点此处](https://repl.it/MivX)。

## 我的解决方案(下)

不管怎样，我回头看了看我的解决方案，心想，“Ryan，这看起来不太好。我必须想象对每个字母运行`blob.count(letter)`是这种情况下最差的性能(blob 中有 27 个字母* n 个字符，对每个字母遍历整个 blob)。看起来像本那样做更有效率，那就是循环遍历 blob 一次，并计算一路上的每个字母。所以我试了一下。

```
def efficient_message(letters, blob)
  # counter = {a: 0, b: 0, c: 0...}
  counter = letters.product([0]).to_h

  # run through blob once only
  blob.each_char { |c| counter[c] += 1 }

  # sort and trim off everything after _
  counter
    .sort_by(&:last)    # sort by the count
    .map(&:first)       # grab just the letter key into an array
    .reverse
    .join
    .split('_')
    .first
end 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，没那么漂亮，但希望更快。正在被解释的 Ruby 比大多数编译过的语言都要慢，所以这应该有所帮助。(所以我想...)

## 比较性能

这种优化值得吗？我需要弄清楚。幸运的是，Ruby 内置了一个很棒的基准测试库。(哦鲁比，有什么是你做不到的？)

```
require 'benchmark'

# ... My code above

Benchmark.bmbm do |bm|
  bm.report("Naive: ") { 1000.times { naive_message(letters, contents) } }
  bm.report("Efficient: ") { 1000.times { efficient_message(letters, contents) } }
end 
```

Enter fullscreen mode Exit fullscreen mode

`Benchmark`有一个名为`bmbm`的方法，它运行一次试运行，然后运行第二次实际运行。这有助于消除垃圾收集器的任何开销性能消耗。让我震惊的是:

```
~\desktop> ruby .\secret_word.rb
Rehearsal -----------------------------------------------
Naive:        0.047000   0.000000   0.047000 (  0.039974)
Efficient:    0.484000   0.000000   0.484000 (  0.481631)
-------------------------------------- total: 0.531000sec

                  user     system      total        real
Naive:        0.031000   0.000000   0.031000 (  0.038011)
Efficient:    0.483000   0.000000   0.483000 (  0.478715) 
```

Enter fullscreen mode Exit fullscreen mode

“高效”版本比“简单”版本慢大约 10 倍！nooo！“但是，为什么？”你问。“怎么会这样？”我也有同样的问题。

## 算法剖析

Ruby 有一个内置的分析器，但是简单的谷歌搜索告诉我有一个更好的选择:`ruby-prof`。在短暂的`gem install ruby-prof`之后，我又开着白色货车回来了。(查看 [Ruby-Prof 文档](https://github.com/ruby-prof/ruby-prof)了解更多信息)。

```
require 'ruby-prof'

# ... The previous code

RubyProf.start
1000.times { naive_message(letters, contents) }
result = RubyProf.stop

RubyProf::FlatPrinter.new(result).print(STDOUT)

RubyProf.start
1000.times { efficient_message(letters, contents) }
result = RubyProf.stop

RubyProf::FlatPrinter.new(result).print(STDOUT) 
```

Enter fullscreen mode Exit fullscreen mode

为了清楚起见，我在下面添加了标题。

```
Naive:
===================
Measure Mode: wall_time
Thread ID: 3259000
Fiber ID: 20752200
Total: 0.066000
Sort by: self_time

 %self      total      self      wait     child     calls  name
 50.00      0.033     0.033     0.000     0.000    27000   String#count
 18.18      0.059     0.012     0.000     0.047     1000   Enumerable#sort_by
 10.61      0.007     0.007     0.000     0.000   136000   Integer#<=>
 10.61      0.040     0.007     0.000     0.033     1000   Array#each
  3.03      0.002     0.002     0.000     0.000     1000   Array#reverse
  3.03      0.066     0.002     0.000     0.064     1000   Object#naive_message
  1.52      0.001     0.001     0.000     0.000     1000   Array#first
  1.52      0.001     0.001     0.000     0.000     1000   Array#join
  1.52      0.001     0.001     0.000     0.000     1000   String#split
  0.00      0.066     0.000     0.000     0.066        1   Global#[No method]
  0.00      0.066     0.000     0.000     0.066        1   Integer#times

* indicates recursively called methods

Efficient:
==============
Measure Mode: wall_time
Thread ID: 3259000
Fiber ID: 20752200
Total: 0.688000
Sort by: self_time

 %self      total      self      wait     child     calls  name
 93.60      0.644     0.644     0.000     0.000     1000   String#each_char
  2.04      0.025     0.014     0.000     0.011     1000   Enumerable#sort_by
  1.16      0.008     0.008     0.000     0.000   136000   Integer#<=>
  0.58      0.005     0.004     0.000     0.001     1000   Array#map
  0.44      0.688     0.003     0.000     0.685     1000   Object#efficient_message
  0.44      0.003     0.003     0.000     0.000     1000   Array#reverse
  0.44      0.003     0.003     0.000     0.000     1000   Array#product
  0.44      0.003     0.003     0.000     0.000     1000   Array#to_h
  0.29      0.003     0.002     0.000     0.001     1000   Hash#each
  0.29      0.002     0.002     0.000     0.000     1000   String#split
  0.15      0.001     0.001     0.000     0.000    27000   Array#last
  0.15      0.001     0.001     0.000     0.000    28000   Array#first
  0.00      0.688     0.000     0.000     0.688        1   Global#[No method]
  0.00      0.000     0.000     0.000     0.000     1000   Array#join
  0.00      0.688     0.000     0.000     0.688        1   Integer#times

* indicates recursively called methods 
```

Enter fullscreen mode Exit fullscreen mode

据我所知，`String#count`方法是超级优化的，而`String#each_char`是相对昂贵的操作(它必须创建一个 blob 长度的数组！).所以，从长远来看，使用更快的`String#count`多次遍历 blob 字符串最终会更有性能。费了这么大劲才产生了一个*高效的*解决方案。

## 总结起来

不管怎样，我希望你能在经历了这样的过山车之后恢复正常的心率。一定要在 [Ben 的帖子](https://dev.to/benhayehudi/solving-a-job-application-code-challenge-30d)上分享你的解决方案。此外，由于这原本是一种面试代码难题，如果你曾经面试过别人，**我希望得到任何关于我的解决方案或相关演示的反馈**！这和你对申请人的期望相似吗，还是我漏掉了什么重要的东西？

感谢阅读！

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com)*