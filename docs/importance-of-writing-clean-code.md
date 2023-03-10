# 编写干净代码的重要性

> 原文：<https://dev.to/mohitrajput987/importance-of-writing-clean-code>

编程是一种告诉另一个人他想要计算机做什么的艺术
——唐纳德·克努特

如果你说得好，人们会喜欢听你说话。沟通是一门艺术，它可以让某人爱上你，也可以给你批评。在编程领域也是如此。如果你写了可怕的代码，你将不能告诉另一个程序员你为什么要写它。这是一个残酷的现实，你在最后期限的压力下工作，这导致了匆忙编写代码。但是请记住这个事实，一个糟糕的代码库会让你的生活变得更加困难。

*“任何人都能写出计算机能理解的代码。只有优秀的程序员才能写出人类能够理解的代码。”*

程序员通常想学习不同的编程语言。在当今世界，有一种错误的观念，认为知道一些技术或最新技术的人是英雄。但是请相信我，如果在一天结束的时候，你不能欣赏你自己的代码，那么学习 Java、CSS、AngularJS、Perl、Ruby、Python、COBOL 是没有意义的。最后，你变成了“万能的杰克，无所不能的主人”。不仅仅是技术；是代码让软件变得强大。

## 这里有一些写干净代码的好处-

**1。你的代码是你的责任-** 你正在写一个代码，六个月后它可能会变得不一样。您无法识别自己的代码。你现在很困惑，告诉自己，“我到底为什么要写这个？我想做什么？你再次努力去理解你自己的代码。如果你遵循最佳实践，你可以节省很多时间。
2**。你在一个团队-** 工作，作为员工或联合创始人；你在一个团队中开发软件。如果你的团队成员不能通过阅读你的代码来理解你的逻辑，你就没有做好。同样的情况发生在你身上，如果你的团队成员没有写好代码，你将会浪费你宝贵的时间去解决冲突，区分&合并代码和理解逻辑。不要给你的团队成员或客户一个几乎保持完整的代码。
**3。易于维护和扩展-** 如果你在开发项目时没有遵循一个模式，六个月后你会觉得很难添加一个新的模块或者改变现有的模块。如果客户告诉你在学生的个人资料中添加一个新的属性，你应该知道有多少班级会受到影响。例如，如果您遵循一个好的 ORM 方法，您只需要在 student 的模型类中添加一个新字段，并添加一些小代码来添加一个新属性。
**4。容易调试-** 你将开发一个从外面看起来很漂亮的项目。在部署和用户交互之后，您会发现用户无法使用名为 a 的功能。如果您持续使用该功能超过 2 分钟，它就会崩溃。现在，您将如何调试它？你的方法是什么？如果这个特性有单独的类或方法，如果您已经正确地完成了日志记录，那么您可以一步一步地调试它并解决确切的问题。如果你只是把所有的东西都写成一个文件，没有日志，也没有方法，你最终会陷入困境。
**5。如果你在为你的客户开发一个项目，你不是产品-** 的所有者；您在项目完成后部署构建。客户仅仅是这个版本的项目的所有者吗？不。客户实际上为源代码付钱给你。如果他以后想修改它，那么他应该可以轻松地使用它。

## 这里有一些干净代码编写的最佳实践

**1。命名约定-** 你能说出这一行代码在做什么吗-

```
int a = b(x); 
```

这个 a，b，x 人靠的是什么？谁能知道为什么要编写这样的代码呢？但是如果同样的代码是这样写的-

```
int salary = getSalary(employee); 
```

现在任何人都知道有一个方法，它将 employee 对象作为参数，并以整数形式返回他的薪水。
**2。模块化-** 编写可重用的代码。比如，不要在需要员工工资的地方到处写工资计算代码。只要写在一个地方，创建一个方法，到处都用。您节省了代码行、刻度和扩展时间，创建了容易理解、最小化错误的机会。使用 OOPS 概念。创建类，编写通用代码，使用继承，并为每个不同的任务创建方法。
**3。创建房子的地图-** 在做房子的时候，你说让我们先做房子，我们看看里面有没有变化。我们会摧毁它并再次制造它吗？有必要先建一个房子的地图。所以不要只是开始写代码。你必须有一个写得很好的文档，里面有图表、用例、顺序图，这样你才能明白你到底在做什么。从第一天开始就保持清晰有助于你写出好的代码。有一个好的建筑师会帮助你开发一个令人兴奋的项目。
**4。保持它简单和小-** 在第一点中，方法 getSalary 应该只计算薪水并返回它。它不应该做任何其他事情，即计算他在过去一年带薪休假的次数。你应该有一个单独的方法来计算假期，如果你在计算薪水的时候需要的话，从 getSalary()方法中调用它。
**5。选择正确的工具-** 为正确的任务选择正确的工具，让工作变得简单、省时、写得好。例如，您正在创建一个动态 java web 项目；你会觉得很难创建文件夹嵌套，编译，并创建清单。您也可能在文件命名和编码中犯错误和拼写错误。如果您使用 Eclipse IDE，您将获得已经创建的结构，这将帮助您在正确的文件夹和文件中编写代码。您将看到错误的原因，您可以轻松地编译，轻松地创建 WAR 文件，轻松地调试。
**6。成为最好的艺术家-** 你是作者，你是编写代码的艺术家。每天改进你的代码编写。每天都是新的一天，来提高你昨天学到的东西。每天改进你的编码。每天都努力让它变得更干净、漂亮、小巧。做最好的写源代码的艺术家。

当你开始接受这种想法时，你告诉自己，“这可以更好”。你把它做得越来越好，然后开始欣赏你的代码。您开始为将 if-else 转换为三元运算符而感到自豪，这仅仅是为了节省三行代码。我希望这篇文章能激励你开始接受这种思想。从今天开始，看看有什么不同。

阅读[编码最佳实践第一部分(命名约定&类设计原则)](https://dev.to/mohitrajput987/coding-best-practices-part-1-naming-conventions--class-designing-principles)来学习一些编码的最佳实践。

### 看了一些我最近开发的 app 的案例分析-

[Jokaamo](https://www.engineerbabu.com/blog/2017/04/jokaamo-services-doorstep/)
T3】CadVise