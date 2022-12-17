# 重新发现品牌:规则背后的力量

> 原文：<https://dev.to/roperzh/rediscovering-make-the-power-behind-rules-7hk>

我曾经认为 makefiles 只是列出 shell 命令组的一种便捷方式；随着时间的推移，我了解到它们是多么强大、灵活和功能齐全。这篇文章揭示了一些与规则相关的特性。

注意:所有这些对 GNU Makefiles 来说都是有效的，如果你需要支持 BSD Makefiles，你会发现在添加新特性的同时还缺少一些特性。感谢 [zge](https://lobste.rs/u/zge) 给的指针

## 规则

规则是指示`make` *如何*和*何时*应该构建称为*目标*的文件的指令。目标可以依赖于其他名为*先决条件*的文件。

您在*方法*中指导`make`如何构建目标，这只不过是一组要执行的 shell 命令，按照它们出现的顺序一次执行一个。语法如下:

```
target_name : prerequisites
    recipe 
```

Enter fullscreen mode Exit fullscreen mode

一旦您定义了一个规则，您就可以从命令行通过执行:
来构建目标

```
$ make target_name 
```

Enter fullscreen mode Exit fullscreen mode

一旦构建了目标，`make`足够聪明，除非至少有一个先决条件发生了变化，否则不会再次运行该配方。

## 更多关于先决条件

先决条件表明两件事:

*   何时应该构建目标:如果先决条件比目标新，`make`假定应该构建目标。
*   执行顺序:由于先决条件可以依次由 makefile 上的另一个规则构建，它们也隐式地设置了规则的执行顺序。

如果您想定义一个顺序，但又不想在先决条件改变时重建目标，那么您可以使用一种特殊的先决条件，称为 *order only* ，它可以放在普通先决条件之后，用管道(`|`)隔开

## 图案

为了方便起见，`make`接受目标和先决条件的模式。模式是通过包含`%`字符来定义的，这个通配符可以匹配任意数量的文字字符或空字符串。以下是一些例子:

*   `%`:匹配任意文件
*   `%.md`:匹配所有扩展名为`.md`的文件
*   `prefix%.go`:匹配所有以`prefix`开头，扩展名为`.go`的文件

## 特殊目标

有一组对`make`有特殊意义的目标名，叫做*特殊目标*。

您可以在[文档](https://www.gnu.org/software/make/manual/make.html#Special-Targets)中找到特殊目标的完整列表。根据经验，特殊目标以一个点开始，后面跟着大写字母。

以下是几个有用的例子:

**。假冒的**:指示`make`该目标的先决条件被认为是假冒的目标，这意味着`make`将总是运行它的配方，而不管是否存在具有该名称的文件或者它的最后修改时间是什么。

**。默认**:用于任何没有找到规则的目标。

**。忽略**:如果您为`.IGNORE`，`make`指定先决条件，那么`make`将忽略它们的配方执行中的错误。

## 换人

当您需要通过指定的变更来修改变量值时，替换非常有用。

替换的形式为`$(var:a=b)`，其含义是获取变量`var`的值，用该值中的`b`替换单词末尾的每个`a`，并替换结果字符串。比如:

```
foo := a.o
bar := $(foo:.o=.c) # sets bar to a.c 
```

Enter fullscreen mode Exit fullscreen mode

*注:特别感谢[路易斯·拉韦纳](https://twitter.com/luislavena/)让我知道了替代的存在。*

## 归档文件

归档文件用于将多个数据文件收集到一个文件中(与 zip 文件的概念相同)，它们是用`ar` Unix 实用程序构建的。`ar`可用于创建任何目的的档案，但已被`tar`大量取代，用于除[静态库](http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html)之外的任何其他目的。

在`make`中，您可以使用归档文件的单个成员作为目标或先决条件，如下所示:

```
archive(member) : prerequisite
    recipe 
```

Enter fullscreen mode Exit fullscreen mode

## 最后的想法

关于 make 还有很多需要探索的地方，但至少这是一个开始，我强烈建议您查看一下[文档](https://www.gnu.org/software/make/manual/make.html)，创建一个哑 makefile，然后就开始玩吧。