# 让无效变得不可能——选择正确的数据模型

> 原文：<https://dev.to/frosnerd/making-the-invalid-impossible---choosing-the-right-data-model-9e6>

## 简介

在我之前的文章中，我谈到了软件设计的复杂性。我们讨论了一个例子，说明选择一个简单的数据结构如何使您的代码更容易理解和维护。

当应用程序处理数据时，复杂性也受到应用程序可能处于的不同状态数量的巨大影响。允许的值组合越多，其中一些值无效的可能性就越大。如果幸运的话，无效状态可能会引发一条错误消息。如果你运气不好，这可能会导致未被发现和/或意想不到的行为。

在这篇博文中，我们将看看如何通过选择正确的数据模型来使无效状态成为可能。这些例子将用 [Scala](https://www.scala-lang.org/) 编写，但是它们可以很容易地移植到任何其他编程语言。

## 状态复杂

构建无状态应用程序是一个很好的实践。然而，在某个地方总会有状态，即使它只存在于连接无状态服务的消息队列中。状态通常是基于真实世界的特定领域表示来实现的，即作为领域对象。这些域对象通常存储在某种数据结构中，并表示为一种类型。

您拥有的域对象越多，它们越复杂，可能的值组合就越多。如果您存储一组客户的姓名、地址和性别，那么您已经有了几乎无限量的可能组合。这里我们在一个对象中只有三个字段。想一想你过去曾经工作过的领域模型。可能的值有多少种不同的组合？其中有多少是有效的？如果您考虑无效的组合，在与您的数据交互时，您是否将它们考虑在内？您确定您捕捉到了所有可能的无效组合吗？

管理状态不是一件容易的事情。如果你的领域很复杂，你将会有一个复杂的领域模型。大多数情况下，这种状态爆炸是无法避免的。然而，你可以做一些事情来减少进入无效状态的机会:避免它！

## 建模你的状态

让我们看一个例子。假设您是一名教授，正在开发一个应用程序来存储学生的课程成绩。每个学生都有一个学生证和一个名字。注册这门课程的学生应该参加最后的考试，在那里他们会得到一个分数。等级可以是从`A`到`F`的任何值。每个考试都会有一个 ID。参加课程的学生不一定要参加考试。

你可能想到的第一个模型可能是:

```
case class Student(
  id: String,
  name: String
)

case class CourseResult(
  id: String,
  students: Seq[Student],
  grades: Seq[String]
) 
```

Enter fullscreen mode Exit fullscreen mode

看起来有用。我们可以储存我们需要的所有信息。参加考试的学生和他们的成绩都存储在一个列表中，通过索引连接。第一个学生的年级是年级序列中的第一个年级。

## 避免无效状态

看着上面的第一个实现，你能看出什么可能出错吗？我们的考试结果类型有哪些可能的实例？让我们创建一个由两个学生参加的考试:

```
CourseResult(
  id = "2017-CS101",
  students = Seq(Student("1", "Frank"), Student("2", "Ralph")),
  grades = Seq("B", "A")
) 
```

Enter fullscreen mode Exit fullscreen mode

好吧。下面这个例子呢:

```
CourseResult(
  id = "2017-CS101",
  students = Seq(Student("1", "Frank"), Student("2", "Ralph")),
  grades = Seq("B")
) 
```

Enter fullscreen mode Exit fullscreen mode

拉尔夫似乎没有成绩。这是因为他报名了却没有参加考试吗？还是因为出现了编程错误，丢了一个成绩？

也许我们需要在构造考试结果实例时添加一个需求，如果学生和成绩列表的大小不同，抛出一个异常:

```
case class CourseResult(
  id: String,
  students: Seq[Student],
  grades: Seq[String]
) {
  require(students.size == grades.size,
    "There need to be as many grades as there are students.")
} 
```

Enter fullscreen mode Exit fullscreen mode

再次尝试构造一个无效的实例将会产生以下错误:

```
java.lang.IllegalArgumentException: requirement failed:
There need to be as many grades as there are students. 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但是如果试图创建无效状态甚至无法编译不是更好吗？我们如何改变实现，使我们不需要依赖运行时异常？一个好的方法是有一个学生和年级配对的列表，或者一本字典来查找每个学生的成绩。选择字典还可以确保每个学生只能注册一次课程。如果你使用了一系列的配对，你必须检查是否有重复。

```
case class CourseResult(
  id: String,
  participations: Map[Student, String]
) 
```

Enter fullscreen mode Exit fullscreen mode

问题解决了，没有学生就没有成绩，反之亦然。但是等等，我们有这样的情况，一些学生注册了课程，但是没有参加考试。我们能做的就是简单地存储它们，并将等级设置为`null`。但是谁阻止你也把考试 ID 设置成`null`。还是学名？

这是允许使用`null`值的语言的常见问题。它增加了更多可能的状态，这些状态在领域中大多数时候是没有意义的。处理可选分级的更好方法是使用 [`Option`](https://en.wikipedia.org/wiki/Option_type) 类型(在其他语言中也称为`Maybe`)。一个`Option`只是另一个值的容器，这个值也可能是空的。我建议避免使用`null`值或者切换到没有`null`的编程语言。

```
case class CourseResult(
  id: String,
  participations: Map[Student, Option[String]]
) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以允许 Ralph 不参加考试，但仍然注册:

```
CourseResult(
  id = "2017-CS101",
  participations = Map(
    Student("1", "Frank") -> Some("B"),
    Student("2", "Ralph") -> None
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们来看看成绩。等级可以从`A`到`F`。然而，无论如何都有可能构建这样一个参与:

```
Student("1", "Frank") -> Some("X") 
```

Enter fullscreen mode Exit fullscreen mode

我们如何避免这种情况？比起添加前提条件和要求，我更喜欢让编译器帮助你。由于可能的等级数量有限(而且相当少)，我们可以简单地列举它们。

```
sealed trait Grade
case object A extends Grade
case object B extends Grade
case object C extends Grade
case object D extends Grade
case object E extends Grade
case object F extends Grade

case class CourseResult(
  id: String,
  grades: Map[Student, Option[Grade]]
) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以编写一个函数来计算一个学生是否通过了使用模式匹配的课程:

```
def passed(g: Grade): Boolean = g match {
  case F => false
  case _ => true
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在移除了`case _ => true`分支(或者首先忘记实现它)，编译器甚至会警告我们:

```
warning: match may not be exhaustive.
It would fail on the following inputs: A, B, C, D, E
       def passed(g: Grade): Boolean = g match { 
```

Enter fullscreen mode Exit fullscreen mode

在 Scala 中实现枚举时，我推荐看一看 [Enumeratum](https://github.com/lloydmeta/enumeratum) ，这是一个非常强大的枚举实现，集成了许多序列化库。

在对我们的初稿做了一些修改后，我们最终得到了下面的第一个最终版本

```
case class Student(
  id: String
  name: String
)

sealed trait Grade
case object A extends Grade
case object B extends Grade
case object C extends Grade
case object D extends Grade
case object E extends Grade
case object F extends Grade

case class CourseResult(
  id: String,
  grades: Map[Student, Option[Grade]]
) 
```

Enter fullscreen mode Exit fullscreen mode

当然，这可能不是完美的解决方案。如果你的需求改变了，你可能也需要改变你的模型。改变模型总是与重构工作联系在一起。根据使用范围的不同，在对`CourseResult`定义进行更改时，您可能需要接触许多其他服务。虽然在这个例子中，我们只关注改变以避免无效状态，但是考虑可扩展性当然也很重要。

## 结论

在这篇文章中，我们讨论了状态复杂性的问题。我们看到了一些例子，说明只调整一些细节，比如变量类型，就已经可以大大降低复杂性。使无效状态成为不可能会减少运行时的错误，因为编译器会支持你。

当然，我们看到的问题已经简化了，你可能已经自动完成了我提到的许多事情。尽管如此，我认为了解你的模型使无效状态成为可能的情况是很重要的。即使这只是一个小细节，比如选择`Set`而不是`List`，如果你不想有重复的话。

大多数时候没有完美的解决方案。选择一个模型来代表现实世界将永远是一个近似值。在可用性、可维护性和可扩展性方面会有所取舍。根据您的功能性和非功能性需求找到正确的平衡非常重要。

现在我很好奇你的看法。如果你是教授，你会选择哪种实现？你会自己实现还是交给一个打工的学生；)?请在评论中告诉我！