# PHPUnit 数据提供者的收益

> 原文：<https://dev.to/jeroendedauw/yield-in-phpunit-data-providers-9l>

最初我开始创建一个关于 PHP 生成器的通用帖子，这是 PHP 5.5 中引入的一个特性。然而，由于我总是无法想出一些使用发电机的好例子，我决定写这篇小帖子，重点介绍一种很酷的用法。

## PHPUnit 数据提供者

一个常用的 [PHPUnit](http://phpunit.de/) 特性是[数据提供者](https://phpunit.de/manual/current/en/writing-tests-for-phpunit.html#writing-tests-for-phpunit.data-providers)。在数据提供程序中，您指定一系列参数列表，使用数据提供程序的测试方法会为每个参数列表调用一次。

通常，数据提供程序是用一个数组变量创建的，其中填充了参数列表。例子(包括糟糕的命名):

```
/**
 * @dataProvider provideUserInfo
 */
function testSomeStuff( string $userName, int $userAge ) {}

function provideUserInfo() {
    $return = [];

    $return[] = [ 'Such Name', 42 ];
    $return[] = [ 'Very Name', 23 ];
    $return['Named parameter set'] = [ 'So Name', 1337 ];

    return $return;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里不太好的一点是，你有一个变量(显式状态)，你修改它(可变状态)。更实用的方法是直接返回一个保存参数列表的数组。然而，如果您的参数列表创建比本例中的更复杂，需要状态，这可能不起作用。当需要这种状态时，你最终会变得更加复杂，并且更有可能被`$return`变量咬到。

## 使用产量

您可能没有意识到的是，数据提供者不需要返回数组。他们需要返回一个`iterable`，因此他们也可以返回一个`Iterator`，并通过扩展返回一个`Generator`。这意味着您可以如下编写上面的数据提供者:

```
function provideUserInfo() {
    yield [ 'Such Name', 42 ];
    yield [ 'Very Name', 23 ];
    yield 'Named parameter set' => [ 'So Name', 1337 ];
} 
```

Enter fullscreen mode Exit fullscreen mode

看不到明确的状态！

如果我能克服自己的*懒惰*(提示提示:)

*最初发布在我的[博客](https://www.entropywins.wtf/blog/)上，名为 [Yield in PHPUnit 数据提供商](https://www.entropywins.wtf/blog/2017/10/09/yield-in-phpunit-data-providers/)。*