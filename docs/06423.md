# 你改变了代码，但没有重构代码。

> 原文：<https://dev.to/gonedark/you-changed-the-code-you-didnt-refactor-the-code>

昨天在 Twitter 上有一个关于 [Laravel 框架](https://github.com/laravel/framework)的代码贡献的[好讨论。它以一些关于“重构”和“改变”代码之间区别的好问题结束。](https://twitter.com/JacobBennett/status/884776645743267845)

虽然我想重点讨论这些区别，但让我们首先关注代码的变化。

下面是原始代码:

```
 public static function before($subject, $search)
    {
        if ($search == '') {
            return $subject;
        }

        $pos = strpos($subject, $search);

        if ($pos === false) {
            return $subject;
        }

        return substr($subject, 0, $pos);
    } 
```

和“重构”的代码:

```
 public static function before($subject, $search)
    {
        return empty($search) ? $subject : explode($search, $subject)[0];
    } 
```

一个漂亮干净的内胆。所有测试都通过了，代码也合并了。

具有敏锐测试眼光的开发人员可能已经注意到了一个问题，但是大多数人注意到我引用了 *refactored* 。

那是因为这段代码没有被*重构*，而是被*改变了*。让我们回忆一下“重构”的定义。

> 重构软件而不改变其可观察的行为

在这种情况下，因为新代码的行为不同于原始代码，所以可观察到的行为发生了变化。

它的行为有何不同？

这需要敏锐的测试眼光，但一个现成的例子是当`$search`是`0`时。原始代码将在`$subject`中搜索，并返回出现`0`的之前的字符串*。而新的代码会随着`$subject`提前返回。不一样的行为。*

不幸的是，现有的测试没有抓住这一点。因此，是贡献者发现了这个*改变的行为*——他们后来这么做了，[提交了一个缺失测试的补丁](https://github.com/laravel/framework/pull/19995)。这样做之后，这就成了一个真正的重构和很好的贡献！

然而，这引出了另一个有趣的问题——既然所有现有的测试都通过了，那么最初的贡献是一次成功的重构吗？

鉴于重构和测试之间的共生关系，一些人认为测试就是需求。所以如果所有的测试都通过了，你就满足了要求。

我认为那是一个滑坡。对我来说,“重构”的定义再次通过它自己的问题提供了答案——我们改变了*可观察的*行为了吗？

在这种情况下，是的——我们可以观察到行为发生了变化。所以最初的贡献不是真正的重构，尽管通过了测试。

尽管如此，我认为围绕重构和测试还有一些其他有趣的地方。我将在以后的文章中探讨这些问题。现在，要注意你是在真正地“重构”代码，而不是“改变”代码。