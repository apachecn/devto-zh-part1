# 现代化的 Java——语言特性列表(第 1 部分)

> 原文：<https://dev.to/martinhaeusler/modernizing-java---a-language-feature-wish-list-part-1-eik>

近年来，编程语言进步很大。有数不清的口味和概念，而且每天都在增加。我见过并积极地使用过相当多的语言，但是过了一段时间后，我发现自己又回到了 Java。尽管我个人认为 Java 是最干净的语言之一，具有极其严格和清晰的语义，但有时它会让人觉得有点过时，我希望 Java 有一些其他语言的特性。在这篇文章中，我想介绍一个“正确使用语言特性”的概要。这个列表当然是 stronlgy 固执己见的，但也许它包含了一个你到目前为止还没有意识到的概念。

> 这是一系列帖子中的第一篇。为了更容易阅读，我决定把它们分开。

# 特征来自[延伸](https://www.eclipse.org/xtend/documentation/)

Xtend 是一种非常有趣的语言。它自认为是 Java 的扩展，实际上编译成 Java 源码(！)代码。Xtend 带来了许多新的想法。以下是我个人的一些亮点。

## 语法上糖为吸气剂& Setters

我们都知道 Java 中的 getters 和 setters。我们都知道为什么我们需要他们，我们也知道他们有时会很痛苦，特别是在打电话的一方。为什么我不能只写`obj.name`而不写`obj.getName()`，让编译器自己解决剩下的问题呢？嗯，这正是你可以用 Xtend 做到的。如果遇到成员访问(如`obj.name`)，它首先检查是否有匹配的公共成员，如果没有，它寻找一个`get` *属性* `()`方法并调用它。如果您的属性是一个数字，那就更好了，因为这样您就可以使用`obj.value++`和其他属性，它们将被编译成合适的 getter/setter 结构。朴素，简单，有效。这完全是一个编译器特性，不需要在任何地方改变现有的字节码或运行时。为什么 Java 今天不这样做对我来说是个谜。

## 扩展方法

Java 运行时库真的挺好的。然而，传统上这个库被认为是...它所提供的保守。“如果有疑问，就忽略它”似乎是指导原则。结果是，我们有一整支“生产力图书馆”大军，它们提供臭名昭著的`XYUtils`课程(例如`StringUtils`、`IOUtils`...).最著名的是 [Apache Commons](https://commons.apache.org/) 包以及 [Google Guava](https://github.com/google/guava) 。就功能而言，这些助手很棒，但是语法有点反直觉。例如，`StringUtils.leftPad(myString, 10)`远不如`myString.leftPad(10)`直观，而且在第一种情况下，程序员需要**知道**关于`StringUtils`的存在，而在第二种情况下，IDE 的代码完成将只是**告诉**它们。同样，这完全是一个语法问题。编译器来拯救我们了！Xtend 允许您导入一个静态方法作为`extension`(这里是[文档](https://www.eclipse.org/xtend/documentation/202_xtend_classes_members.html#extension-methods) ):

```
import static extension java.util.Collections.*; // note the 'extension' keyword

// ... later in your code ...

myList.max(); // compiles to Collections.max(myList) 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，Xtend 是我所见过的唯一一种能够正确实现这一特性的语言。这就是你处理 utils 的方式。这可能看起来是一个可笑的小变化，但是一旦你习惯了，你就离不开它了。对`javac`来说，这真的是一个唾手可得的果实。我甚至会更进一步，去掉关键字`extension`。在我的当前作用域中，任何具有匹配签名的静态方法都应该有资格被用作扩展方法，在名称冲突的情况下，对象上定义的方法优先。

## 与无课者交谈`.class`

当编写反射代码时，最大的(语法上的)痛点之一是你不断地编写`.class`，例如在`MyClass.class.getName()`中。好吧，废话。**当然**要说到我写`MyClass`时的阶级对象。那么为什么我还要在这里写`.class`？因为解析器少需要一个前瞻？走吧。Xtend 在这方面也有帮助。只写`MyClass.getName()`就够了，它编译到`MyClass.class.getName()`。当然，`MyClass.getName()`也可以指类中定义的`public static String getName()`方法。在 Xtend 中，这个静态方法优先(您可以在 Xtend 中限定访问，使其显式)。但是让我们诚实地说:我还没有在任何 Java 类中定义我的第一个`public static Field[] getDeclaredFields()`方法。另一个小小的改变，朝着开发者的幸福又迈进了一步。

## 全能的`dispatch`

Java(和许多其他语言，包括 C#)中的方法调度有一个问题，这个问题 99%的时候都没有被注意到，但是当它发生的时候，真的会让你头疼。假设你有几个这样的重载方法:

```
public void print(Person p) { ... }

public void print(Student s) { ... }

public void print(Object o) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

(我们假设`Student`扩展了`Person`)。快速测验:下面的代码调用了哪个重载:

```
Object me = new Student();
print(me); 
```

Enter fullscreen mode Exit fullscreen mode

答案是`print(Object o)`。因为 Java 基于**静态**类型(即变量的类型，在本例中为`Object`)而不是**动态**类型(存储在变量中的实际值的类型，在本例中为`Student`)选择方法重载进行调用。这主要是为了提高效率，在大多数情况下，它被称为“单一调度”。但是如果你想要另一个案子呢？你必须这样做:

```
 public void print(Object o) {
  if(o instanceof Student) {
    print((Student)o);
  }else if(o instanceof Person) {
    print((Person)o);
  }else{
    // print the object like before
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码实现了所谓的“双重分派”，编写这段代码是一件**痛苦**的事情。我觉得编译器应该做的是重复和全面的事情。此外，您需要注意首先检查最具体的类型。在 Xtend 中，要实现上述行为，您只需使用`dispatch`关键字:

```
public dispatch void print(Person p) { ... }

public dispatch void print(Student s) { ... }

public dispatch void print(Object o) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

...它将编译成如上所述的`print(Object)`中的`instanceof`级联。感谢编译器为我节省了一些打字！

# 特色来自[锡兰](https://ceylon-lang.org/)

Ceylon 是一种 JVM 异类，即编译成 JVM 字节码的语言。它有很多特性(其中一些有争议)，但它的语言工具集中也有一些精华。

## 流打字

流类型是当今许多语言都使用的一个特性(hello， [TypeScript](http://www.typescriptlang.org/) ！)，但正是在锡兰，我第一次目睹了这一功能的完整形式，我被震撼了。很容易解释它的作用。我们都用 Java 写过这样的代码:

```
for(Object element : myCollection){
   if(element instanceof Dog) {
      ((Dog)element).bark()
   } else {
      // ... do something else
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

你有没有想到过**为什么**在检查完`element`确实是`Dog`类型之后**必须向下转换成上面例子中的`Dog`？这是重复的，它通常意味着引入新的变量(为此你必须选择一个合适的名字...)，而且总体来说写起来就是平平淡淡的**烦人的**。在锡兰，上面的 cast **是不需要的**。**根本**。一旦您通过类型检查验证了存储在变量中的对象属于某个类，当您在条件块中时，编译器会将该变量视为该类型。这个特性我们甚至不需要新的 snytax。我们只是消除噪音。第一次在实践中看到这一点对我来说就像魔法一样——我从来不知道我想要这个，但现在我想要了，我对每次在`instanceof`检查后不得不做的向下投掷都皱眉头。**

## 可空性和严格可空性通过`?`

你知道我在 99%的 Java 方法实现中做的第一件事是什么吗？事情是这样的...

```
public void save(User u){
   if(u == null){
      throw new IllegalArgumentException("User must not be NULL!");
   }
   // do ACTUAL work
} 
```

Enter fullscreen mode Exit fullscreen mode

这被广泛认为是良好的实践，事实也的确如此。在调试器中通过代码库回溯一个`null`值一点也不好玩。然而，在上面的方法中，我指定我想要一个`User`实例。严格来说，`null`不是`User`(这一点你可以说出来，因为`null instanceof User`在 Java 中实际上返回的是`false`)。然而，在 Java 中调用`save(null)`是合法的。Ceylon 提供了一种优雅的方式来解决这个问题。默认情况下，您**不能将`null`作为参数**传递，除非参数**明确允许**使用`null`值。这是通过向参数
追加一个`?`来实现的

```
public void save(User? u){ ... }  // accepts nulls
public void save(User u){ ... }  // compiler rejects (potential) null values 
```

Enter fullscreen mode Exit fullscreen mode

这里的好处是，在第二种情况下，我作为程序员根本不需要执行手动的`null` -ness 检查。编译器在编译时完成。因此，这种方法不仅更安全，而且在运行时更快(T2 ),并在最早可能的时间点(编译阶段)捕捉错误。双赢。

## 编译器检查的反射

这是一个独特的特征，除了在锡兰，我在其他任何地方都没见过。以下是我发现这颗宝石的原始博文:

```
 value d = `value Movie.year`;
    print( d.name); //prints "year" 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`Movie`是一个类。我们反射性地访问**字段** `Movie.year`。Java 的对等词是`Movie.getClass().getField("year")`。那么锡兰怎么样比较好呢？嗯，如果`Movie`实际上**没有**名为`year`的字段，**编译器会报错**。这是反射编码的游戏规则改变者。有多少次你的 hibernate 模板因为字段名拼写错误而无法工作？还是在重构过程中丢失了？给你。

本系列的第一部分到此结束。我希望你喜欢它，也许还学到了一些新的语言特性。Xtend 和 Ceylon 提供了许多其他的东西，我强烈建议去看看，如果不是为了生产，至少是为了体验。

([第二部](https://dev.to/martinhaeusler/modernizing-java---a-language-feature-wish-list-part-2-7c0)现已上线。)