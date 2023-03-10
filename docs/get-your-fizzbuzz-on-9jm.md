# 打开你的(嘶嘶)嗡嗡声

> 原文：<https://dev.to/yechielk/get-your-fizzbuzz-on-9jm>

## 第三幕

学习编码充满了学习的时刻。我深情地回忆起的一件事是我在 FizzBuzz 上的熨斗课程。当 Flatiron 聘请我作为技术蔻驰时，这种记忆得到了极大的强化；FizzBuzz 是我最喜欢帮助新生的实验室之一。

我将解释为什么学习(然后教授)FizzBuzz 的过程如此突出，但首先要说明 FizzBuzz 是什么。

## 什么嘶嘶声？

我知道你听到 FizzBuzz 想到的第一件事大概是这样的:
[![Fizzy Buzzy Was A Hare](img/bf2dc539966107b7290223bc86fbbd3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D35PhgHf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2A7QUZQje9UrsKHNVv.jpg)

FizzBuzz 是一个小游戏，面试官用它来从那些根本不知道如何编码的人中筛选出非常糟糕的编码员。这是一个相当简单的问题，任何对计算机工作原理有最基本了解的人都应该能够解决，然而很明显，有足够多的申请开发人员职位的人不能解决它，这证明了它在面试中的用途。

问题如下:

> 写一个程序打印数字 1-100，但是对于每个是 3 的倍数的数字打印单词`Fizz`，对于每个是 5 的倍数的数字打印`Buzz`，对于每个是 3 和 5 的倍数的数字打印`FizzBuzz`。因此，前 15 个数字的输出如下所示:
> `1, 2, Fizz, 4, Buzz, Fizz, 7, 8, Fizz, Buzz, 11, Fizz, 13, 14, FizzBuzz…`

在这篇文章中，我将用 Ruby 展示解决方案，因为我喜欢 Ruby，我认为它是最漂亮的语言。但是大多数概念可以移植到任何语言。

## 什么不起作用

为了解释 FizzBuzz 的美妙之处，以及为什么我喜欢把它教给新程序员，让我来演示一个新程序员可能会想到的第一个下意识的解决方案。

```
(1..100).each do |i|
    if i % 3 == 0
        puts "Fizz"
    elsif i % 5 == 0
        puts "Buzz"
    elsif i % 3 == 0 && i % 5 == 0
        puts "FizzBuzz"
    else
        puts i
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

用英语说:

> 对于 1-100 范围内的每个数字，检查以下内容:该数字是否能清晰地分成三个印刷`Fizz`，否则，是否能清晰地分成五个印刷`Buzz`，否则，检查它是否能清晰地分成三个和五个印刷`FizzBuzz`。

似乎很简单，不是吗？如果你把这些指令给了一个人，如果他们把它搞砸了，你会很生气，这是可以理解的。但显然，这对于计算机来说还不够清楚。如果你把上面的代码片段给一台计算机，你会看到一个很好的数字列表，有很多嘶嘶声和嗡嗡声混合在一起，但没有一个嘶嘶声。为了理解为什么，我们必须看看人类如何看待指令和计算机如何看待指令之间的差异。

当一个人看一段代码时，我们试图把它看作一个整体。这段代码在做什么？它想达到什么目的？为什么要写？电脑没有那种大视野；计算机只会逐行分析代码，并执行给定的指令。

让我们试着通过计算机的眼睛来看看上面的代码。

首先，计算机看数字 1。它通过我们为它概述的步骤。它能分成 3 份吗？不。好吧，什么都别做。接下来是不是分成 5 份？不。好吧，什么也别做。接下来是不是分为 3 和 5？不。好吧，什么都别做。接下来，打印号码，知道了。

然后计算机对数字 2 重复这个过程；看起来差不多。

到了第三点，过程就不同了。电脑试着把它分成三份；有用！太好了！让我们印出嘶嘶声。下一个数字，等等。

现在让我们看看当我们到达 15 号时会发生什么。记住，计算机一次只分析一行，所以计算机尝试将 15 除以 3，它成功了，计算机很高兴，它打印出 Fizz，然后继续下一个数字，永远不会到达被告知尝试除以 3 和 5 的那一行。

## 解

幸运的是，这可以通过简单地将最后一个条件移到前面来解决，这样计算机将在分别尝试之前尝试将数字分成 3 和 5，就像这样:

```
(1..100).each do |i|

  if i % 3 == 0 && i % 5 == 0
    puts "FizzBuzz"
  elsif i % 3 == 0
    puts "Fizz"
  elsif i % 5 == 0
    puts "Buzz"
  else
    puts i
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 越多越模糊

现在，如前所述，FizzBuzz 是一个初学者的问题，任何花了一些时间编码的人应该能够不用太多思考就能解决它。但是在编程中，解决问题的方法从来都不是只有一种。

[![All this talk of Fizz and Buzz is really giving me a lift](img/73d07ae1dbfda86ea3952e2a83993500.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3CYIMUtd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AIruB2296CkQ2aEDK.gif)

我想分享一个解决 FizzBuzz 的不同方法，一个在我看来更漂亮的方法。这个解决方案采用了一个稍微不同的方法:

```
(1..100).each do |i|

  output = ""
  output += "Fizz" if i % 3 == 0
  output += "Buzz" if i % 5 == 0
  output = i if output.empty?

  puts output
end 
```

Enter fullscreen mode Exit fullscreen mode

在英语中:

> 对于 1-100 范围内的每个数字，我们从一个空字符串(`""`)开始，然后如果数字能被 3 整除，我们将`"Fizz"`添加到字符串中，如果数字能被 5 整除，我们将`"Buzz"`添加到字符串中，如果此时字符串仍然是空的(只有当数字不能被 3 或 5 整除时才会出现这种情况)，我们将把空字符串换成数字。

请注意，在这种方法中，我们从不检查数字是否能被 3 和 5 整除，我们只是将 Buzz 添加到现有的字符串中，如果数字不能被 3 整除，它将是一个空字符串，如果它能被 3 整除，它将是一个等于 Fizz 的字符串。

## 但是规模大吗？

所以现在我们解决了问题，但是作为程序员，我们一直在努力改进。

我们的解决方案是可行的，问题是它不能很好地扩展。假设我们想做同样的事情，但是对于 1 到 50 之间的所有数字呢？500?5000?我们必须为每一个都写一个单独的 FizzBuzz 程序。

当然，解决方案是编写一个 FizzBuzz 方法，我们可以提供一个数字，并让它对从 1 到我们提供的数字的每一个数字运行 FizzBuzz 逻辑。我们甚至可以给它一个默认参数，这样如果我们不提供一个数字，它将打印 FizzBuzz 的 1-100。

看起来应该是这样的:

```
def fizzbuzz(num = 100)
  (1..num).each do |i|

    output = ""
    output += "Fizz" if i % 3 == 0
    output += "Buzz" if i % 5 == 0
    output = i if output.empty?

    puts output
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在 Ruby(和其他语言)中有很多方法可以解决 FizzBuzz，欢迎在评论中添加你最喜欢的。

* * *

*这篇文章是从我的博客 [Rabbi On Rails](https://blog.yechiel.me/get-your-fizz-buzz-on-543f2a327a9d) 交叉发布的。
你可以在那里了解更多关于我的编程之旅，或者在 Twitter 上关注我 [@yechielk](https://twitter.com/yechielk)*