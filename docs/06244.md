# 在 F#中何时使用有区别的 Union vs 记录类型

> 原文：<https://dev.to/josegonz321/when-to-use-a-discriminated-union-vs-record-type-in-f>

随着我的[函数式编程冒险](http://www.mindbodysouldeveloper.com/2017/01/07/intro-functional-programming-resources/)的继续，我遇到了我不熟悉的术语。今天，我们将看看什么时候使用有区别的联合和记录类型。

我坚信学习一件事的最好方法是把它教给别人。

它会巩固你的知识。

(我正在学习 F#，在我变得太擅长它之前，我想与你分享我的初学者笔记。希望它能帮助你理解 F#和函数式编程。)

## 有区别的联合

让我们跳过所有无聊的行话。这是我能找到的最简单的解释

> 可以枚举的可能[数据]值。

一些例子:

```
type Status =
| Active
| Inactive
| Disabled

type Answer =
| Yes
| No 
```

`Answer`以上只能是`Yes`或`No`。

除了那两个具体值，不可能是别的。

它的工作原理似乎类似于 C#枚举。

## 记录类型

再次跳过所有的行话:

> [用于]由多个属性描述的复杂数据，如数据库记录或一些模型实体

这些相当于 C#语言中的类。但是我觉得它们更像是结构而不是类。

一些例子:

```
type User = { FirstName: string; LastName : string; Age: Int; }

type Cookie = { Name: string; Color: string; } 
```

## 何时使用有区别的联合 vs 记录类型

> 把它想象成一个记录是‘和’，而一个有区别的并集是‘或’。

这是关键。

**记录** = **和**
和**区别开的工会** = **或**

示例:

```
type MyRecord = { myString: string
                  myInt: int } 
```

以下值可以是`string`或`int`，但**不能同时是**:

```
type MyUnion = 
| Int of int
| Str of string 
```

这是虚拟游戏中另一个歧视工会的例子:

```
type Game =
| Title
| Ingame
| Endgame 
```

你当时只能在一个区域。

你不可能既在`Title`屏幕里又是`Ingame`。
你不可能既在`Ingame`又在`Endgame`。

请将此视为单选按钮的**选项。**

## 关闭思绪

如果您一直跳到最后，请确保至少阅读了**何时使用有区别的 Union vs 记录类型**一节。

歧视工会听起来很吓人。但是把它想象成多种选择，你的价值可以是 T1。

最简单的比较就是单选按钮的选项。

你怎么想呢?你如何在 F#中定义一个`discriminated union`和一个`record`类型？

*最初发布于:
【http://www.mindbodysouldeveloper.com/】T2*

## 资源

*   [堆栈溢出](https://stackoverflow.com/questions/17291932/when-to-use-a-discriminate-union-vs-record-type-in-f)