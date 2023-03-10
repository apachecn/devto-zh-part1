# 如何写技术博客

> 原文：<https://dev.to/yelluw/how-to-do-technical-blogging>

## 快速汇总

在这篇文章中，我将向你展示如何写技术文章。这篇文章详细描述了一篇技术文章的结构，提供了一些有用的例子，以及一些我从经验中学到的重要技巧。

## [T1】简介](#intro)

软件开发人员开始写博客的原因之一是分享他们的技术知识和经验。一些人没有意识到的是，分享技术知识是他们必须在基本博客技能之上发展的技能。在这篇文章中，我将告诉你如何写技术文章。

## 一个技术岗位的结构

技术文章的结构与普通博客文章不同。一篇技术文章要求你向读者提供元信息，因为他们可能会决定实现你所解释的任何东西。一般来说，技术职位的结构如下:

*   头条新闻

*   快速小结

*   要求

*   介绍

*   多步骤解释

*   例子

*   技巧

*   摘要

让我来解释一下这些是什么。

### 标题

一篇技术文章的标题非常重要。它是内容的一部分，告诉读者如何做某事。这就是为什么许多技术标题使用“如何做 X”的方法。它清晰、直截了当，没有任何令人困惑的术语(你最不想做的事情就是让你的读者困惑)。如果你还没有注意到，这篇文章的标题就是这种方法的一个实现。

其他非常有用的标题类型如下:

`Writing a $technology $thing.`

`Developing with $technology.`

`Testing $technology with $thing.`

请注意，这里有一个非常简单的工作模式。有用的技术标题往往以动词开头，快速解释文章内容，读者不必花时间阅读。

#### 快速汇总

快速总结并不总是必要的，但它在大多数时候肯定是有用的。用 2-3 句话概括文章的内容可以节省读者的时间。把快速总结想象成一个更长的副标题。标题和快速摘要之间的关系很重要。在标题停止的地方，快速摘要应该继续。阅读这篇文章的标题和摘要，看看这个转变有多顺利。

### 要求

需求是读者为了成功阅读和应用文章所涵盖的内容而需要具备的东西。如果你正在编写一个 Python 教程，那么需求应该是:使用的 Python 版本，一个文本编辑器，等等。要非常具体，尽可能包括版本。让我用上面的例子告诉你怎么做:

### 要求:

`Python 3.6.1`

`Text Editor (Vim was used for the examples)`

`Operating System (Linux, MacOS, Windows) with version information (Ubuntu 16, MacOS 10.12, Windows 10 Professional)`

需求很重要，因为软件因版本和平台而异。清楚地说明你用了什么让读者知道他们是否准备好跟随。如果没有，他们可以在继续之前做好准备。这是一个很多技术文章都失败的领域，让读者自己去解决任何问题。一个好的技术作家将读者从这种沮丧中解救出来。

### 简介

引言解释了你将在技术文章中涉及的内容。它是解决任何非技术性问题的地方。人们通常开始在介绍中包含技术细节。这不是一个好主意，因为介绍可能会很长，而且没有很好的结构。我讨厌人们在介绍中包含晦涩的技术细节，因为这会立刻让我迷惑。这里有一个很好的样本介绍:

`Python has become the defacto glue language in software development. In it's newest version (3.6.1), the language has evolved into a powerful tool that is easy to pick up. Python is included as a default in most Linux distros, and in MacOS. Such widespread adoption means you will probably not need to install anything in order to run Python on your machine. Follow along as I explain how to use Python's F-Strings in a Linux environment.`

注意介绍是如何通过提供一些简单的背景信息来帮助你取得成功的，而没有包括任何技术细节，这些技术细节在以后是必要的，并且在需求中没有预先列出。

#### 多步解释

这是你详细解释事情的地方。我强调多个步骤，因为把事情分解成更小的部分会让你作为一个作者更容易处理，并让读者快速找到他们停下来的地方。要在读者的头脑中描绘出好的精神画面，还需要多个步骤。

解释中的每一步应该有多细？越细越好。有时它们可能只有一句话。然而，长度不如范围重要。让我们稍微谈一谈范围。

**范围**在技术岗位和编程语言中是一样的。你有**本地**和**全球**范围。全球范围意味着在某个地方讨论的信息适用于其他所有东西。本地范围意味着正在讨论的信息适用于你正在阅读的任何内容。

解释中的每一步都有局部和全局范围。这意味着每个步骤中讨论的信息首先对步骤本身很重要，其次是对一般帖子很重要。但不是反过来。**作用域有优先权**并且非常重要。在解释的任何部分有错误的范围水平都会使读者困惑。

有多种方法可以在视觉提示的帮助下局部封装范围。视觉提示是图形或印刷元素，让你让读者知道特定区域内的一切都与其自身密切相关(局部范围)，但与全局范围关系不大。让我给你看一个例子:

```
#######
#
# This is a globally encapsulated scope. 
# Note that everything that I write here is assumed to be directly related.
# 
# I wrote this post as a result of a Tweet!
#
######

######
#
# This is a locally encapsulated scope. What I write here is separate from the rest.
#
#  #####
#  #
#  # How are you enjoying this post?
#  #
#  #
#  # #####
#  # #
#  # # Yes, this totally looks like OOP. It is not a coincidence. :)
#  # #
#  # #####
#  # 
#  #####
#
###### 
```

Enter fullscreen mode Exit fullscreen mode

我想让你注意的一件事是，最深层次的范围仍然与其他两个相关，但在交流思想时有优先权。人们倾向于先阅读最深的范围。这是非常重要的一点。当涉及到范围时，人们遵循的顺序是首先是最深的。使用上面的例子，将倾向于按以下顺序阅读上面的文本:

*   首先->是的，这看起来完全像 OOP。这不是巧合。:)
*   第二->你觉得这篇文章怎么样？
*   第三->这是一个本地封装的作用域。我在这里写的和其他的是分开的。

总之，把事情分解成更小的步骤，并提供视觉提示，告知读者范围。保留与特定步骤局部相关的内容。当有疑问时，将事情分解并提供视觉线索。拥有多个小(微)步骤比少数几个大步骤要好。

### 例子

如今提供例子是必要的。人们通常会跳过所有其他内容，直接看例子。这就是为什么在例子中重复每件事是重要的。是的，这意味着包括需求和多步解释作为例子的一部分。您不会将文本复制并粘贴到示例中，但是您会提供一个更简洁的版本。

例子是技术文章中最重要的部分，因为人们会利用它们来理解你所讨论的内容。事实上，人们对一篇技术文章的反应与所提供的例子有多好直接相关。你能提供的例子越多越好。但是，请确保提供不同长度和类型的示例。你不想忘记包括一个“你好，世界”的例子，但是你也不想它是你包括的唯一例子。使用 Python 的 F 字符串，让我给你看一些例子，你可以从中学习。

用作介绍的非常简单的例子:

```
# python 3.6.1 on MacOS 10.12 
# Hello world with F-Strings 
name = "World"
print(f"Hello, {name}") 
```

Enter fullscreen mode Exit fullscreen mode

展示一些具体技术细节的简单示例(本地范围):

```
# python 3.6.1 on MacOS 10.12 
# Using Python's F-Strings in functions 
def hello(name):
    return f"Hello, {name}"

def print_hello(name):
    print(hello(name)) 
```

Enter fullscreen mode Exit fullscreen mode

一个更复杂的例子是:

```
# python 3.6.1 on MacOS 10.12 
# Implementing FizzBuzz with Python's F-Strings
# and ternary operators 
def fizzbuzz():
  for i in range(1, 101):
      print(f"FizzBuzz with number {i}") if i % 3 == 0 and i % 5 == 0 else None
      print(f"Fizz with number {i}") if i % 3 == 0 else None
      print(f"Buzz with number {i}") if i % 5 == 0 else None
      print(f"Now on number {i}") 
```

Enter fullscreen mode Exit fullscreen mode

重点是增加示例的复杂性，以指导用户的学习之旅。上面的每个例子都建立在最后一个的基础上，并且共享一个主题:Python 的 F 字符串。

## 提示

我决定把我这些年来学到的一些技巧包括进来。其中一些并不总是适用，但知道这些是有用的。

1.  如有疑问，请解释。多解释总是有用的。如果你使用视觉提示来提供更深入的解释，你可以避免写文字墙。通过用视觉提示将它们分开，如果人们能快速理解，就可以避免阅读它们

2.  避免使用低对比度的颜色作为视觉线索。深灰色背景上的浅灰色字体不太好。使其易于阅读。

3.  让多人阅读。反馈很重要。这是大多数人理解的东西和让所有人困惑的东西之间的区别。由于它们的性质，很容易写出令人困惑的技术文章。

4.  亲亲。简单点，笨蛋。当一小部分就可以了，不要包括大量的细节。

5.  学习高度赞扬的技术文章或文档。人们喜欢称赞 Stripe 的文档。找出原因，并将所学应用到你的岗位上。

6.  用图形来分解文字。我通常坚持使用尽可能少的图形，但它们可能是有用的。这取决于讨论的是什么。大多数情况下，你必须自己绘制图形。不要让它们太鲜艳。坚持 2-5 种颜色，保持简单。

7.  不要将源代码放在图形或图像中。人们希望将代码复制并粘贴到他们的编辑器中。

8.  允许他们运行文章中的代码。这并不总是可能的，但在这种情况下是有用的。

9.  尽可能使用锚。人们通常会将你的页面加入书签。在你文章的特定区域提供锚点，这将允许他们在停止阅读的地方添加书签。对例子有用。

10.  最好是浅色的。幽默是没有帮助的。保持玩笑的轻松，远离源代码。

## 总结

摘要是添加附加信息链接的地方，感谢那些帮助写文章的人，并添加任何可能对读者有用的内容。我喜欢使用以下格式:

`You have now learned about Python's F-Strings. If you'd like to learn more about them, please visit the following links:`

`- Pep 498, https://www.python.org/dev/peps/pep-0498/`

`I must thank all of the people who made this post possible. If you need Python development services, please visit yelluw.com and use the contact form to get in touch with me. Feel free to email me at pryelluw@gmail.com with questions.`

上面的格式把与文章相关的重要链接放在第一位，所有手续放在第二位，联系方式放在第三位。我总是把联系方式放在最后，因为人们倾向于在那里寻找。

* * *

您现在已经学会了如何撰写技术博客文章。了解这一点非常有用，因为公司倾向于雇佣能够清晰沟通的软件开发人员。这也可以是一种补充收入的方式。我必须感谢 Stephanie Hurlburt 提供的想法和支持。在推特上关注她，因为她很棒。

如果您需要有人为您撰写技术文章，请随时通过 [Yelluw 的网站](http://yelluw.com/#prospect-contact-form)联系我。你也可以给我发电子邮件到 pryelluw@gmail.com 的 T2。你也应该关注 Yelluw 的推特账号[@ yellow media](https://twitter.com/yelluwmedia)和我的个人账号[@ pryellow](https://twitter.com/pryelluw)。我也可以提供短期和长期的软件开发合同。我目前的重点是 Python、Django 和 Javascript。