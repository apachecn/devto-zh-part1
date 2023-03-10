# 科特林在行动

> 原文：<https://dev.to/slashgear_/kotlin-in-action-4k63>

JetBrains，面向困在洞穴中的 Java 开发人员，要求 Eclipse 除了提供一套开发工具和 Java ide(intelli j)之外，还于 2011 年开始创建新的编程语言。**充满了遗憾，这种语言，哈迪·哈里在 duMix-it 2016 年提出的 Kotlin 给我留下了深刻的印象。**

2016 年 4 月 21 日星期四至 22 日星期五，将在里昂举行第六届混合会议。这项技术吸引了 600 多名与会者，为开发人员和敏捷开发人员举办了一个盛大的聚会，他们不仅能够参加会谈、讲习班、会议，而且还能与来自世界各地的爱好者围绕美味的 suzette 煎饼进行交流。

在等待着这篇文章的回归文章——it，让我们回到——这篇文章的主旋律科特林。当时我只知道名字，所以我对 demr hadi harriri 题为" Kotlin : ready for production "的演讲非常感兴趣。在对 Jetbrains 项目的语言历史和目标作了相当简要的介绍之后，我们很快就可以了解到，通过引入 kotlin，国际公司试图通过将 C#的优势和最简洁的语言结合起来，来满足 Java 开发人员的需求。如果该公司对 Oracle 发布更高要求的新 Java 版本失去了希望，则应考虑 sedereman。

## 简洁好

如上所述，简洁是 Jetbrains 创造 Kotlin 语言的明确意愿和目标。在 Java 中，我们很容易发现自己在任何地方都扮演了替身。我们想到的是 getter/setter，这是一种可以很好总结的功能。当然，Lombok 这样的插件可以通过注释来减少所有代码量。

*为了给大家看一个简洁的例子，这里有一个比较 Java 中非常简单的对象和 Kotlin 中完全相同的对象。*

Java 版本:

```
public class Person implements Cloneable{

  private String name;

  private Integer id;

  public Person(String name,Integer id){
    this.name = name;
    this.id = id;
  }

  public String getName(){
    return name;
  }

  public void setName(String name){
    this.name = name;
  }

  public Integer getId(){
    return id;
  }

  public void setId(Integer id){
    this.id = id;
  }
  @Override
  public boolean equals(Object obj) {
    if (obj == this) {
      return true;
    }
    if (obj == null || obj.getClass() != this.getClass()) {
      return false;
    }
    Person guest = (Person) obj;
    return id == guest.id && (name == guest.getName || (name != null && name.equals(guest.getName()))));
  }

  @Override
  public int hashCode() {
    final int prime = 31;
    int result = 1;
    result = prime * result + ((name == null) ? 0 : name.hashCode());
    result = prime * result + id;
    return result;
  }

  @Override
  public String toString(){
    return"Person(name="+name+",id="+id")";
  }

  @Override
  public Object clone(){
     Person person = null;
        try {
            person = (Person) super.clone();
        } catch(CloneNotSupportedException cnse) {
            cnse.printStackTrace(System.err);
        }
        person.name = (String) name.clone();
        person.id = (Integer) id.clone();
        return person;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

版本科特林:

```
dat class User(val name: String, val id: Int) 
```

Enter fullscreen mode Exit fullscreen mode

来自 Object 的函数(toString、equals、-什么)在 Kotlin 文件中的" class "前面添加关键字" data "。

## 有趣的功能

在“Mix-IT”会议上，我发现了一些我认为在这篇文章中真正感兴趣的功能。这份清单并非详尽无遗，但应能让您大致了解各种可能性。

## 跟班

要在科特林注册一个班级，可以去掉垃圾/烘干机，可以定义一个主要的构造者。此外，您甚至可以使用 var 和 val(只读)关键字来定义类属性的可变性。

```
class Person(val firstName: String, val lastName: String, var age: Int) {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 不带引号的函数语句(内嵌)

我们可以声明不带引号的函数，它相当紧凑。我们失去了一些可读性，但我们赢得了空间。

```
(fun add(i: Int, j:Int) = i + j) 
```

Enter fullscreen mode Exit fullscreen mode

## 条件比较简单

不要再用“箭头运算符”来代替 when。

```
when (x) {
  1 -> print("x == 1")
  2 -> print("x == 2")
  else -> { // Note the block
    print("x is neither 1 nor 2")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于循环来说，没有什么特别值得注意的，但在“if”操作中可以看到非常实际的补充。

```
val max = if (a > b) {
    print("Choose a")
    a
  } else {
    print("Choose b")
    b
  } 
```

Enter fullscreen mode Exit fullscreen mode

if 块的最后一个表达式是块的值。避免在块外部初始化变量，并避免在每个分支中进行 sa encoding。您可以看到哪一个语句结束运算符“；”隐藏(实际上是可选的)

## 告别“零点异常”

Kotlin 旨在避免 null 引用的危险，并防止 NullPointerException 和 null-check 的出现。

```
var a: String = "abc"
a = null // compilation error
To allow nulls, we can declare a variable as nullable string, written String?:

var b: String? = "abc"
b = null // ok

b?.length 
```

Enter fullscreen mode Exit fullscreen mode

如果故意将变量设置为 null，则不能将该变量设置为 null。Kotlin 还引入了“调用安全运算符”，允许在对变量调用函数之前测试变量的无效性。

## 的功能“安静”

在 Kotlin 中，可以在对象外部声明函数。这对于 Java 来说似乎很奇怪，但此功能可能非常实用，尤其是在声明 main deson projet 函数时。

在对象外部声明的函数可以在任何位置调用，如下所示:

```
fun hello(){
  print("hello")
}

fun main(){
  ::hello()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 功能编程治理所有这些

功能范式当然存在于语言中。执行所有常见操作(map、reduce、forEach 等)。-什么)

```
var sum = 0
ints.filter { it > 0 }.forEach {
  sum += it
}
print(sum) 
```

Enter fullscreen mode Exit fullscreen mode

## 莱斯【扩展功能】

在 Kotlin 中，可以用简单的方法通过函数“扩展”类。因此，可以很容易地添加方法而无需继承。

```
fun MutableList<Int>.swap(index1: Int, index2: Int) {
  val tmp = this[index1] // 'this' corresponds to the list
  this[index1] = this[index2]
  this[index2] = tmp
} 
```

Enter fullscreen mode Exit fullscreen mode

此处的关键字“this”用于访问扩展对象。

## 互操作性

这种新语言提出的问题来得相当快。我们可以轻松地从 Java 切换到 Kotlin 吗？我们的 codeKotlin 是否能与所有我们已经拥有的 Java 代码配合使用？我们现在的工具是否适合科特林发展

## Les IDE

很明显，JetBrains 把自己的产品放在了 Kotlin 的使用上。事实上，我们可以用他们著名的智慧来发展这种语言。蛋糕上的樱桃，甚至可以用免费的社区版来构思。还有一个 Eclipse 插件。但是，NetBeans 没有正在运行的插件包。

## 爪哇

Kotlin 还提供了与所有 Java 库的兼容性，这可能是 JetBrains 最有力、最明显的论点。因此，您建立的程式码可以在版本 6 到版本 8 的 JVM 上运作。

因此，我们可以充分利用 Kotlin 语言的优势，同时将虚拟机向后推 10 年。最后，不需要对 java avec 与 Kotlin 进行任何更改。此外，对 Kotlin 的 Java 方法调用完全透明。

## Javascript

是的，JetBrains 不想忽视 Javascript 最近的发展动态。因此，可以将 aubuild JavaScript 语言作为目标，使 Kotlin 应用程序在 NodeJS 或浏览器中运行。因此，此功能需要测试。也许我们这里有另一个剧本？...但这在我眼里还是小玩意。

## 安卓

由于我只做了几个 Android 实验室，所以我不一定对环境有全面的了解，但我记得我遇到了获取移动应用程序 sise 所需的 Java 代码行数的问题。

事实证明，现在可以从 Kotlin 的 Google 推荐的 IDE(如 Android Studio)开发 Android 手机应用程序，供业馀用户测试。

## Spring Boot

最后，可以看出 Kotlin 对 pringing 项目的赞助者感兴趣，特别是塞巴斯蒂安·德勒泽里昂在题为“与 Kotlin 一起开发 Spring Boot applications”的文章中。看到这种语言周围如此强烈的反响有点放心，而且我个人认为尝试在 Spring Boot 的语境中使用 Kotlin，使我对这个问题有一个更客观的看法。

此外，spring initializr 最近在 Kotlin 引入了 Spring Boot 项目生成选项。

不要犹豫去网站上测试这个语言，并就这个语言提出自己的意见而对于我来说，我会很快为我的一个项目尝试一下

你可能会感兴趣的还有‘t0’Hadi Hari’t1’的 Github 上的其他资源