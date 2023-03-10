# 单元测试很简单

> 原文：<https://dev.to/scalawilliam/unit-testing-is-simple>

*https://www.scalawilliam.com/unit-testing-is-simple/*

单元测试难吗？

不，不是的。你甚至不需要一个测试框架。你甚至不需要在单独的文件或类中编写测试。

你只需要用代码 1 退出[。这足以使 Jenkins 构建和 Makefile 失败！](http://tldp.org/LDP/abs/html/exitcodes.html)

为什么单元测试有用？

*   当您更改代码时，防止重新访问。
*   将预期的输入和输出记录为代码。
*   用测试驱动开发取代容易出错的 [println 驱动开发。](http://codebetter.com/jeremymiller/2006/06/01/tdd-and-debugging/)

> **你考什么？**当逻辑“足够复杂”时，我知道它可能会在 6 个月后崩溃，因为“那一个小变化”——或者我会花时间用打印语句来构建它。[例子来自我自己的代码](https://github.com/ActionFPS/dashboard/blob/e757931de22d82edd20e4ef5ad3b2df56d433a16/test_build.py#L82)。

在这里，我将向您展示用四种语言进行单元测试的最基本方法:Bash、Python、Node.js、Scala，只是为了证明这一点。

这些例子是针对一段非常基本的代码，并且有意做到极简，以证明您不需要使用测试框架来开始——尽管当您扩展您的应用程序时，测试框架是非常必要的。

## 痛击

这里有一个 bash 脚本`inc.sh`，包含一个基本断言和一些主要代码:

```
#!/bin/bash

increment() {
    echo $(("$1"+1))
}

# If this assertion fails, the whole thing quits 
[ 3 -eq $(increment 2) ] || exit 1

# Increment a stream of numbers
while read n; do increment $n;
done 
```

Enter fullscreen mode Exit fullscreen mode

如果我们跑:

```
$ seq 1 5 | bash inc.sh
2
3
4
5
6
$ echo $?
0 
```

Enter fullscreen mode Exit fullscreen mode

退出代码是 0。但是如果我们打破这个函数，把 1 换成 2，我们得到:

```
$ seq 1 5 | bash inc.sh
$ echo $?
1 
```

Enter fullscreen mode Exit fullscreen mode

## 巨蟒

这在任何语言中都很容易实现。 [Python 断言](https://docs.python.org/2/reference/simple_stmts.html#the-assert-statement):

文件`inc.py`中的中断测试:

```
def increment(n):
  return n + 2

assert increment(2) == 3 
```

Enter fullscreen mode Exit fullscreen mode

```
$ python inc.py
Traceback (most recent call last):
  File "inc.py", line 4, in <module>
    assert increment(2) == 3
AssertionError
$ echo $?
1 
```

Enter fullscreen mode Exit fullscreen mode

## Node.js

带有内置在中的[断言。](https://nodejs.org/api/assert.html#assert_assert_equal_actual_expected_message)

文件`inc.js`中的中断测试:

```
const assert = require('assert');

function increment(n) {
  return n + 2;
}

assert.equal(2, increment(1)); 
```

Enter fullscreen mode Exit fullscreen mode

```
$ node inc.js

assert.js:81
  throw new assert.AssertionError({
  ^
AssertionError: 2 == 3

...
$ echo $?
1 
```

Enter fullscreen mode Exit fullscreen mode

## [斯卡拉](http://scala-lang.org/)

带有 assert。

```
def inc(n: Int) = n + 2
assert(inc(1) == 2) 
```

Enter fullscreen mode Exit fullscreen mode

```
$ scala inc.scala
java.lang.AssertionError: assertion failed
$ echo $?
1 
```

Enter fullscreen mode Exit fullscreen mode

# 这个为什么重要？

您可以从几个断言开始，然后逐步发展到合适的测试套件。

# 那么测试框架是干什么用的呢？

让测试更有条理。您将获得:

*   组织
*   详细的错误消息
*   详细的测试报告
*   简洁的断言和人类可读的语言
*   增量/TDD 类型的开发

# 结论

现在，您没有理由不在代码中编写至少几个测试。