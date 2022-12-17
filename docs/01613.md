# 静态类是邪恶的

> 原文：<https://dev.to/wrongabouteverything/static-classes-are-evil-44jg>

尽管有些语言，例如 PHP，没有静态类，但这个概念仍然存在。完全由静态方法组成的类实际上与静态类是一回事。

除了静态类是[过程化的](http://www.yegor256.com/2015/02/20/utility-classes-vs-functional-programming.html)和它们的客户端是[不可测试的](http://michalorman.com/2012/04/stop-writing-static-methods/)(好吧，Java 和 PHP 中有一些漏洞，但我不想提及它们)，它们还有更多问题。

## 阶层往往由大到巨

由于具有静态方法的类与对象无关，所以它们不知道自己是谁，应该做什么，不应该做什么。界限很模糊，所以我们只是写一个又一个指令。不完成任务是很难停下来的。这是不可避免的命令和非面向对象的过程。

## 隐藏依赖关系

代码可读性更差。课堂上有什么？数据库查询，一些激烈的计算，电子邮件发送？你无法控制班上有多少人。这里一个静态方法，那里一个——这就是我们新的上帝对象。当你意识到的时候，已经太晚了。

## 凝聚力低

因此，这个班级越来越缺乏凝聚力。如果这个类有很多依赖项，那么很可能它做了过多的事情。为了公正起见，我应该说大量依赖的可能原因是它们处于较低的抽象层次。因此，在更高层次的抽象中组合依赖关系可能是一条出路。

## 紧耦合

静态方法意味着它们可以从任何地方被调用。可以从很多上下文中调用它们。他们有很多客户。因此，如果一个类需要在静态方法中实现一些小的特殊行为，您需要确保没有其他客户端被破坏。所以这样的重用根本行不通。我可以将它与 noble(和失败的)尝试组合和[重用微服务](https://medium.com/@wrong.about/wrong-ways-of-defining-service-boundaries-d9e313007bcc)进行比较。产生的类太通用，完全不可维护。这导致整个系统紧密耦合。

## 举例

作为示例，让我们考虑客户的财务余额(取自[支付服务提供商示例](https://medium.com/@wrong.about/example-of-service-boundaries-identification-e9077c513560))。它有所有提到的缺点，看起来像这样:

```
class FinancialBalanceUtils
{
    static public function reserveBalance()
    {
        if (!self::shouldReserve()) {
            return;
        }
        self::assertAllConditionsAreMet();
        self::queryFinancialBalanceForCurrentClient();
        $result = self::checkFinancialBalance();
        if (!$result) {
            return false;
        }
        self::reserveFinancialBalanceForCurrentClient();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，我们从头来考察一下。

我们不确定从哪里调用它，因为它可以从任何地方调用。所以我们需要确保这个方法真的应该被执行，因此有了 *shouldReserve()* 方法。
我们又一次不确定它是从哪里来的！所有前提条件都满足了吗？没有人知道，所以我们绝对必须验证这个—*assertAllConditionsAreMet()*方法。
然后我们通过*query financial balance current client()*方法从数据库中获得大量参数的财务余额，因为查询和数据库表为每个需要财务余额的客户服务。好的，我们仍然不确定我们得到的财务平衡是否正常。我们需要检查那里有什么。
a 最后，我们确实使用*reservefinancialbalancefurcurrentclient()*保留了余额。

我省略了日志记录、错误处理和小的代码错误，只保留了基本的部分。而且已经太大了。这个类本身就是一个隐藏的依赖项，它由隐藏的依赖项组成。这种方法执行了多少数据库查询？我不知道。你还怀疑这个类做的比它应该做的多吗？
据说避免复制粘贴会导致完全不可维护的、超级通用的代码，编辑起来非常可怕，这值得吗？

## 试试 OOP 吧

*   [识别你的对象](https://medium.com/@wrong.about/how-to-avoid-anemic-domain-model-5e1c3e6fe4d0)。关注“是什么”，而不是“如何”。关注对象，而不是过程。
*   当对象被识别时，首先要明确所有的依赖关系，并限制它们的数量。每个方法限制在五个以内。为什么是五个？我不知道，只是听起来很合理。六点已经太多了。当超过五个的时候，你的班级可能想做更多的事情。或者，很可能，这些依赖关系的抽象级别太低。无论如何，显式依赖给了你一个让你的设计更加坚实的机会。
*   不要过早的一概而论，记住[三](https://en.wikipedia.org/wiki/Rule_of_three_(computer_programming))法则。首先只实现一些特定的场景。

* * *

下面是我[中帖](https://medium.com/@wrong.about/static-classes-are-evil-or-make-your-dependencies-explicit-af3e73bd29dd)的交叉贴版本。