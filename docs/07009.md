# 林挺作为 Python 的轻量级缺陷检测

> 原文：<https://dev.to/sethmlarson/linting-as-lightweight-defect-detection-for-python>

当许多人想到林挺时，他们会想到它是如何通过迫使开发人员坚持约定的代码风格来提高代码的可读性和可维护性的。这确实是将“过梁”作为构建过程的一部分的主要优点之一，但不是唯一的优点！

为了说明这一点，这里有一个示例场景，它是我所面临的真实场景的简化版本。

# 场景

假设您的一位同事向您提交了一份评估请求。从表面上看，持续集成通过了所有的测试，代码有 100%的覆盖率，这是很好的开端。他们提交的代码包括一个函数，该函数接受一个名字列表，并处理除第一个名字之外的所有名字。该函数如下所示:

```
def process_all_names_except_the_first_one(names):
    if len(names) == 0:
        raise ValueError('There should be at least one name!')

    # Gets the first name as a list.
    name = names[:1]

    # Processes all the other names.
    others = [process_name(name) for name in names[1:]]

    return name + others 
```

Enter fullscreen mode Exit fullscreen mode

这不是最有趣的功能，但它服务于它的目的。它甚至使用了列表理解，非常 Pythonic！但是从这个帖子的标题就知道，这里有问题。一些读者甚至会发现一些看起来可疑的东西！列表理解使用`name`作为它的内部迭代变量，它是在列表理解之外定义的！

所以你要做正确的事情，在这条线上留下一个评论，解释这里有一个问题，并把拉请求标记为需要一些修改。

不久之后，你的同事打电话给你，让你到他们的机器前向你展示变量没有泄漏！他们切换到已经打开的 Python 控制台，并键入以下示例:

```
$ x = 1
$ l = [x for x in range(10)]
$ print(x)
1 
```

Enter fullscreen mode Exit fullscreen mode

令你惊讶的是，它打印出的是`1`而不是`9`！就这么定了，代码按计划运行，你的同事是正确的。您回到您的办公桌，修改您的评审以获得批准，您的同事提交他们的代码。

一个缺陷已经进入了你的代码库。如果您也有持续部署，正如我们所说的那样，它正在交付生产。顾客的抱怨很快就会接踵而来。

# 刚刚发生了什么？

你必须原谅我，因为为了说明这一点，我隐瞒了一些信息。您维护的项目支持 Python 2.7 及更高版本，而您的同事使用的是 Python 3.6 控制台。在 Python 2.x 中，函数**不能正常工作**，变量绑定到列表理解范围之外的名字。[如果你不知道 Python **的这一点，你并不孤单。大约一年前我才知道这件事。**](https://stackoverflow.com/a/4199355)

这个例子很简单，但是你可以想象收到一个很大的拉请求，伴随着大量的代码，这样的小事情很容易被忽略。这个例子恰好也是一个你可以写一个 100%覆盖这个功能并且不会触发缺陷的测试。

因此，当拥有测试套件、100%代码覆盖率、持续集成和代码同行评审时，这个 bug 可能会被忽略。缺失的部分是什么？

# 林挺来救援了！

在这种情况下，什么可以让我们避免发布有缺陷的代码呢？当然，有人会说你的同事应该写更多的测试，或者你应该检查测试以确保它们足够严格。这种想法的问题是，在这种情况下，这肯定就足够了，但这个例子基本上是这种缺陷发生的最简单的例子。

在某些情况下，您将修改或审查遗留代码，其中函数长达数百行，以至于您在审查时甚至看不到原始变量被绑定在哪里。正如我们从许多项目中知道的，可能没有一个详尽的测试套件，甚至可能根本没有测试！这些都是软件项目的现实。我认为，尤其是在这些更棘手的案例中，林挺对在投入最少时间的情况下捕捉潜在缺陷有着巨大的影响。

# Python Lint 工具的首选:Flake8

如果你从未听说过 [Flake8](http://flake8.pycqa.org/en/latest/) ，这是我向林挺 Python 项目推荐的工具。作为一种替代，我也成功地使用了 [Pylint](https://www.pylint.org/) 来完成过去的项目。你应该检查他们两个，但是在这个例子中，我将使用 Flake8。

对于这个例子，我假设您对 Flake8 的工作原理有所了解，它读取并解析您的 Python 源文件，然后编译一个违规列表，并输出它们以及行号和字符号，以便准确定位错误所在。

如果你对 Flake8 不太熟悉，或者想做额外的阅读，我发现[他们的文档](http://flake8.pycqa.org/en/latest/user/index.html)相当不错。GitLab 上提供了[来源，供任何好奇或想提出项目问题的人使用。](https://gitlab.com/pycqa/flake8)

要装 Flake8，我会推荐`pip`。使用以下命令安装 Flake8:

```
$ python -m pip install flake8 
```

Enter fullscreen mode Exit fullscreen mode

### 配置 Flake8 捕捉缺陷

我听到的关于林挺的一个主要抱怨和反对是，要集成到一个大的现有代码库中需要太多的努力。尤其是由不同的团队开发的，要么有不同的代码质量标准，要么根本没有。重构这样一个项目需要付出大量的努力来进行改进，这对于管理人员来说是很难证明的，比如代码质量。

为了避免这个问题，请使用具有更严格配置的 Flake8。我已经创建了一个示例`setup.cfg`文件，它忽略了除了那些可能导致缺陷的违规行为。

```
[flake8]
ignore = E123,E126,E127,E128,E2,E3,E401,E50,W2,W3 
```

Enter fullscreen mode Exit fullscreen mode

应该像这样调用 Flake8:

```
$ flake8 [[SOURCE DIRECTORY NAME]] 
```

Enter fullscreen mode Exit fullscreen mode

如果我们使用 Python 2.7 在我们之前看到的旧函数上运行 Flake8，我们将收到以下冲突:

```
example.py:6:33: F812 list comprehension redefines `name` from line 5 
```

Enter fullscreen mode Exit fullscreen mode

如果 Flake8 是我们构建过程的一部分，这个缺陷会很快被发现并修复，比依赖评审人员了解 Python 2.x 和 3.x 之间的细微差别要快得多。

程序员和评审员同样可以对代码的质量和正确性更有信心。这对每个人来说都是一个胜利。

### 配置 Flake8 为您工作

被忽略的违规列表是从 [Flake8 文档](http://flake8.pycqa.org/en/latest/user/error-codes.html)以及 [pycodestyle 文档](https://pycodestyle.readthedocs.io/en/latest/intro.html#error-codes)中收集的。如果您发现这个被忽略的限制列表不完全符合您的要求，您可以浏览这些文档，并在您的 Flake8 配置中添加或删除违规。

如果您的代码基中需要一个违例，您也可以在代码中使用下面的注释在一个地方取消该违例:

```
imagine_this_is_code_with_a_violation # noqa: F401,E3 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，不会检查该单行是否违反`F401`或任何以`E3`开始的违反

### 你没有忽略所有的空格违规？那不是很时髦吗？

确实，几乎所有的空白违规都可以被认为是风格上的，不太可能导致缺陷。并非所有的空白违规都是如此。

例如，混合制表符和空格。在 Python 2 中这在技术上是允许的，但是会给编辑器带来一系列问题，这些编辑器为制表字符提供了可配置的显示间距。这里有一个例子:

```
# This is how the function would display on your
# editor with 8 character tabs...
def function(a, b, c, d):
    if a:
        if b:
            d += 1
            c += 1
    return c * d

# ...But this is how Python will see it.
def function(a, b, c, d):
    if a:
        if b:
            d += 1
        c += 1 # <--- There's a rogue tab character on this line.
    return c * d 
```

Enter fullscreen mode Exit fullscreen mode

另一个例子是将多个语句放在一行中，这可能会导致读者在没有仔细阅读时误解函数的流程。

```
def function(a, b, c):

    for a in b:

```
 # ... Tons of code ...

    # It's tough to see that this is even an `if`
    # statement without syntax highlighting!
    if c: a += 1

    # ... Tons of code ... 
```

    Enter fullscreen mode 

    Exit fullscreen mode 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
如果项目支持 2.x 和 3.x，使用两个版本运行 Flake8】

因为它是一个运行如此快速的工具，所以我建议在该项目支持的所有 Python 版本中运行 Flake8。这将保证无论 Python 版本如何，都可以捕捉到上面这样的缺陷。

如果你正在寻找一个工具来帮助你做到这一点，我建议你看一看 [tox](https://tox.readthedocs.io/en/latest/) 。

# 总结

林挺不仅仅用于风格检查，它也是发现缺陷的强大工具。您不必使用林挺作为样式检查器来获得这些好处，只需配置该工具来检测那些现在或将来可能成为缺陷来源的违规。使用本指南可能有助于减少与现有代码库集成的时间。

非常感谢 PyCQA 开发了 Flake8。

在 Twitter 上关注我[@ python oasis](https://twitter.com/pythoasis)或者查看我的 GitHub[@ sethmicaellarson](https://github.com/SethMichaelLarson)。