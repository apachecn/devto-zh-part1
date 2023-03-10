# 重新发现 make:自动变量

> 原文：<https://dev.to/roperzh/rediscovering-make-automatic-variables-58n5>

尽管很简单，但在编写重要的 makefiles 时，自动变量非常方便。本文介绍了它们是什么以及如何使用它们。

*注意:如果你需要快速复习规则，请查看 make 系列的[上一篇文章](https://dev.to/roperzh/rediscovering-make-the-power-behind-rules-3367-temp-slug-6534817)。*

## 自动变量

由于[模式](https://monades.roperzh.com/rediscovering-make-power-behind-rules/#patterns)对于定义目标和先决条件非常有用，所以它们也是动态的:每次运行配方时，通配符可以匹配不同的文件，如果您需要在配方中引用它们，这就成了一个问题。

您可以使用自动变量来处理这个问题。顾名思义，`make`在每次执行规则时，根据规则的目标和先决条件，为您*定义一组变量。*

虽然自动变量对模式最有用，但是为了简化概念，下面的例子使用了静态文件名，但是上面描述的也适用于模式。

假设您有以下规则:

```
targetdir/targetfile.o : pre.c predir/pre.h | order-only.c
    # recipe 
```

Enter fullscreen mode Exit fullscreen mode

运行配方时，自动分配以下变量:

| 可变的 | 意义 | 例子 |
| --- | --- | --- |
| $@ | 目标的文件名 | targetdir/targetfile.o |
| $< | 第一个先决条件的名称 | pre.c |
| $? | 比目标更新的所有先决条件的名称 | 取决于上下文 |
| $ | 所有先决条件的名称 | pre.c predir/pre.h |
| $ |  | 所有仅限订单的先决条件的名称 |
| $* | 与隐式规则匹配的词干 | 目标目录/目标文件 |
| $% | 档案成员的姓名([见档案](https://monades.roperzh.com/rediscovering-make-power-behind-rules/#archive-files) | - |

#### 修饰语

有时，您可能只需要变量的目录或文件名，因为这个场景`make`提供了两个修饰符，您可以将它们添加到您的变量中:

| 修饰语 | 意义 | 例子 |
| --- | --- | --- |
| D | 目录部分 | `dir` |
| F | 目录内文件部分 | `simple.c` |

修饰符添加在变量的右边，需要用括号括起来，例如如果`$@`是`targetdir/targetfile.o`:

*   `$(@D)`是`targetdir`
*   `$(@F)`是`targetfile.o`

### 帮手

由于自动变量可能很难记住，这里有一些资源可以帮助你:

#### 假冒的 makefile

一个很好的学习方法是通过实验:创建一个目录，将模拟文件放在那里，并定义您的目标和先决条件，然后，不要在菜谱中做一些有用的事情，只需打印变量:

```
targetdir/targetfile.o : predir/pre.h | order-only.c
  @echo '@: $@'
  @echo '%: $%'
  @echo '<: $<'
  @echo '?: $?'
  @echo '^: $^'
  @echo '|: $|'
  @echo '*: $*' 
```

Enter fullscreen mode Exit fullscreen mode

#### 片段

我发布了可能会派上用场的 [Visual Studio 代码](https://github.com/roperzh/vscode-make-auto-vars)和[崇高文本](https://github.com/roperzh/sublime-make-auto-vars)的代码片段包:它们给了你一个可用变量的列表，后面是对它们用途的简短解释:

[![plugin-example](img/d7a428098092f80af82b3f3288395935.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VnkPm2U---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/4419992/35174851-a1af3c78-fd4f-11e7-9882-ff8f02523ffe.jpg)

注意:如果你正在使用另一个编辑器，有工具可以在代码片断格式之间转换。