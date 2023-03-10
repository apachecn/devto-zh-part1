# 为什么使用尤达条件你可能不应该

> 原文：<https://dev.to/greg0ire/why-using-yoda-conditions-you-should-probably-not>

在我偶然发现[这个现已删除的堆栈溢出问题](https://stackoverflow.com/questions/2349378/new-programming-jargon-you-coined)之前，我已经使用了一段时间的 Yoda 条件，后来[在这篇博文](http://www.dodgycoder.net/2011/11/yoda-conditions-pokemon-exception.html)中备份了这个问题。我记得当时想“这可以节省我很多时间！”。的确，谁能声称他们从来没有写过这样的代码:

```
<?php
if ($name = 'Luke Skywalker') {
   // this piece of code will always be executed
} 
```

Enter fullscreen mode Exit fullscreen mode

那么，尤达条件是如何工作的呢？这基本上是一种下意识的反应:每当你写一个条件时，把不能赋值的操作数放在左边。如果你在实际打算做比较的时候做了一个赋值，这会给你一个错误信息。

在我们的例子中，这给出了以下内容:

```
<?php
if ('Luke Skywalker' = $name) { // Parse error: syntax error, unexpected '='
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果你全押并使用严格的比较，这:

```
<?php
if ('Luke Skywalker' === $name) {
} 
```

Enter fullscreen mode Exit fullscreen mode

在英语中，这被翻译成“如果卢克·天行者是这个名字的话”，如果你来自达戈巴或者尤达实际上来自的任何一个星球，这应该感觉非常熟悉，但是如果你习惯于英语语法，这只是认知负荷，如果你正在编写程序，你应该这样。

我相信它必须成为一种下意识的反应，因为如果它不这样做，它就没有意义:如果你能想到使用 Yoda 条件，那么你肯定能想到检查你是否真的打算在这里写一个作业。你必须不假思索地使用它们。

所以让我们说你发展了膝跳反应。您可能会得到这样的代码:

```
<?php
if ('Luke Skywalker' === $this->getName()) {
} 
```

Enter fullscreen mode Exit fullscreen mode

你看到这里的问题了吗？两个操作数都不能赋值，所以没有好处，但是坏处还是有的。

很快，疾病蔓延到不平等，[甚至在我最喜欢的框架](https://github.com/symfony/symfony-docs/pull/5381)的文档中，你最终会得到像这样不必要的复杂代码:

```
<?php
if (42 < $force) {
} 
```

Enter fullscreen mode Exit fullscreen mode

在挑选尤达之前，有几件事需要深思。

首先，唯一受益于尤达条件的人是你，作家。这只会让阅读您的代码的人的生活变得更加艰难，包括代码审查人员、同事或潜在的贡献者。

此外，如果您测试每个代码路径，您应该能够在问题到达开发管道的下一步之前发现问题。测试可以在这方面有所帮助，特别是如果你在编写实际代码之前编写测试，在一个健康的红绿重构循环中

接下来，代码审查(尤其包括[自我代码审查](https://dev.to/sharpshark28/self-code-review-with-git-add-patch))应该在测试失败时发现错误。

最后，如果你对条件中的赋值没有强烈的需求，你也可以使用一个静态分析器来执行[一个禁止赋值的规则](https://github.com/slevomat/coding-standard/blob/master/README.md#slevomatcodingstandardcontrolstructuresassignmentincondition)。如果你仍然希望能够使用它们，你也可以[阻止尤达条件，只有他们](https://github.com/slevomat/coding-standard/blob/master/README.md#slevomatcodingstandardcontrolstructuresyodacomparison-)。

所以去吧，今天就摆脱尤达的状况，让绝地去死吧。

<figure>

[![see your enemy](img/ba0624788fac3f21c1a4acfc3c2f16d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M2RGSQgQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s2t2zznd0jup1c8jj2y0.jpg)

<figcaption>Image by Daryl Mandryk</figcaption>

</figure>

* * *