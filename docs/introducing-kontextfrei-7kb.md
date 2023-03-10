# “kontextfrei 简介”

> 原文：<https://dev.to/dwestheide/introducing-kontextfrei-7kb>

*这篇文章最初发表在[丹尼尔·韦斯特海德的博客](http://danielwestheide.com/blog/2017/10/31/introducing-kontextfrei.html)上。*

在过去的 15 个月里，我断断续续地在做一个新的图书馆。到目前为止，我对它基本上保持沉默，因为我不觉得它适合更广泛的受众使用——尽管我们已经在生产中成功地使用了一段时间。然而，早在今年 4 月，当我在今年华沙的 ScalarConf 上做了一个关于它的演讲后，我就打破了我的沉默，我在博客上解释了这个库是做什么的，以及为什么我一开始就开始写它。

去年，我参与了一个项目，该项目要求我的团队实现一些 Spark 应用程序。对于他们中的大多数人来说，业务逻辑相当复杂，所以我们试图用测试驱动的方式实现这个业务逻辑，使用属性驱动的测试。

## Spark 应用程序单元测试的痛苦

乍一看，这似乎是一个伟大的匹配。归根结底，Spark 应用程序由 IO 阶段(读取和写入数据源)和数据集转换组成。后者构成了我们的业务逻辑，并且相对容易与 IO 部分分开。它们大多是由纯函数构建的。像这样的函数通常非常适合测试驱动的开发以及基于属性的测试。

然而，一切并不顺利。如果您已经使用 Apache Spark 有一段时间了，这可能对您来说是旧闻，但事实证明，Spark 实际上并没有很好地支持编写真正的单元测试，因此，这可能相当痛苦。事情是这样的，为了创建一个`RDD`，我们总是需要一个`SparkContext`，而得到一个最轻量级的机制是创建一个本地的`SparkContext`。创建一个本地`SparkContext`意味着我们启动一个服务器，这需要几秒钟，而用大量不同的生成输入数据测试我们的属性需要很长时间。例如，可以肯定的是，我们正在失去我们从开发 web 应用程序中所习惯的快速反馈循环。

## 用 kontextfrei 对 RDDs 进行抽象

现在，我们可以将自己限制在只对传递给`RDD`操作符的函数进行单元测试，这样我们的单元测试就不依赖于 Spark，并且可以像我们习惯的那样快速得到验证。然而，这暴露了相当多的业务逻辑。相反，在去年 5 月的 Scala 黑客马拉松上，我开始尝试对 Spark 的`RDD`进行抽象，于是 *kontextfrei* 诞生了。

想法如下:通过对`RDD`进行抽象，我们可以编写不依赖于`RDD`类型的业务逻辑。这意味着我们也可以编写火花不可知的测试属性。任何像这样的 Spark 不可知代码既可以在`RDD`上执行(在实际的 Spark 应用程序和集成测试中会这样做)，也可以在本地和快速的 Scala 集合上执行(这对于开发期间在本地持续运行的单元测试非常有用)。

## 获取库

展示这是如何工作的可能比仅仅用文字来描述更容易，所以让我们来看一个非常简单的例子，传统的*字数*。首先，我们需要向 SBT 构建文件添加必要的依赖项。Kontextfrei 由两个不同的模块组成，`kontextfrei-core`和`kontextfrei-scalatest`。前者是您需要在您的主代码库中抽象出的`RDD`,前者是为了获得一些额外的支持，以便使用 ScalaTest 和 ScalaCheck 编写您的与 RDD 无关的测试。让我们将它们添加到我们的`build.sbt`文件中，以及您无论如何都需要的常见的
Spark 依赖项:

```
resolvers += "dwestheide" at "https://dl.bintray.com/dwestheide/maven"
libraryDependencies += "com.danielwestheide" %% "kontextfrei-core-spark-2.2.0" % "0.6.0"
libraryDependencies += "com.danielwestheide" %% "kontextfrei-scalatest-spark-2.2.0" % "0.6.0" % "test,it"
libraryDependencies += "org.apache.spark" %% "spark-core" % "2.2.0" 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在这个简单的例子中，我们创建了一个 Spark 应用程序，您可以以自包含的方式执行它。在现实世界中，您可以添加`spark-core`作为`provided`的依赖项，并创建一个程序集 JAR，然后传递给`spark-submit`。

## 实现业务逻辑

现在，让我们看看如何使用 *kontextfrei* 实现字数统计应用程序的业务逻辑。在我们的例子中，我们在一个名为`WordCount` :
的特征中定义了我们所有的业务逻辑

```
package com.danielwestheide.kontextfrei.wordcount

import com.danielwestheide.kontextfrei.DCollectionOps
import com.danielwestheide.kontextfrei.syntax.SyntaxSupport

trait WordCount extends SyntaxSupport {

  def counts[F[_]: DCollectionOps](text: F[String]): F[(String, Long)] =
    text
      .flatMap(line => line.split(" "))
      .map(word => (word, 1L))
      .reduceByKey(_ + _)
      .sortBy(_._2, ascending = false)

  def formatted[F[_]: DCollectionOps](counts: F[(String, Long)]): F[String] =
    counts.map {
      case (word, count) => s"$word,$count"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到的第一件事是，`counts`和`formatted`的实现看起来和你用 Spark 的`RDD`类型编程时完全一样。你可以将基于`RDD`的代码复制粘贴到用 *kontextfrei* 编写的程序中。

您注意到的第二件事是，`counts`和`formatted`的方法签名包含一个类型构造函数，声明为`F[_]`，它受到上下文绑定的约束:对于我们在这里传递的任何具体类型构造函数，必须有一个 kontextfrei 的`DCollectionOps` typeclass 的实例。在我们的业务逻辑中，我们不关心什么具体类型的构造函数用于`F`，只要`DCollectionOps`中定义的操作支持它。通过这种方式，我们将我们的业务逻辑从对 Spark 的依赖中解放出来，特别是对烦人的`SparkContext`的依赖。

为了能够使用我们从`RDD`类型中了解到的熟悉语法，我们混合了 kontextfrei 的`SyntaxSupport`特征，但是如果您更喜欢的话，您也可以使用 import 来代替。

## 将我们的业务逻辑插入 Spark 应用程序

最终，我们希望能够拥有一个可运行的 Spark 应用程序。为了实现这一点，我们必须将火花不可知的业务逻辑与应用程序中依赖于火花的 IO 部分结合在一起。在我们的字数统计示例中是这样的:

```
package com.danielwestheide.kontextfrei.wordcount

import com.danielwestheide.kontextfrei.rdd.RDDOpsSupport
import org.apache.spark.SparkContext

object Main extends App with WordCount with RDDOpsSupport {

  implicit val sparkContext: SparkContext =
    new SparkContext("local[1]", "word-count")

  val inputFilePath = args(0)
  val outputFilePath = args(1)

  try {

    val textFile   = sparkContext.textFile(inputFilePath, minPartitions = 2)
    val wordCounts = counts(textFile)
    formatted(wordCounts).saveAsTextFile(outputFilePath)

  } finally {
    sparkContext.stop()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`Main`对象混合了我们的`WordCount`特征以及 *kontextfrei* 的`RDDOpsSupport`，这向编译器证明了我们有一个`DCollectionOps`类型类的实例用于`RDD`类型构造函数。为了证明这一点，我们还需要一个隐含的`SparkContext`。同样，我们也可以使用导入来代替混合这个特征。

现在，我们的`Main`目标是做一些 IO 并将我们的业务逻辑集成到其中。

## 编写火花不可知测试

到目前为止一切顺利。我们已经将业务逻辑从对 Spark 的依赖中解放出来，但是我们从中获得了什么呢？好了，现在我们也能够以一种火花不可知的方式编写我们的单元测试了。首先，我们定义一个`BaseSpec`，它继承了 kontextfrei 的`KontextfreiSpec`，并混合了来自 *kontextfrei-scalatest* 和 scalatest 本身的一些好东西:

```
package com.danielwestheide.kontextfrei.wordcount

import com.danielwestheide.kontextfrei.scalatest.KontextfreiSpec
import com.danielwestheide.kontextfrei.syntax.DistributionSyntaxSupport
import org.scalactic.anyvals.PosInt
import org.scalatest.prop.GeneratorDrivenPropertyChecks
import org.scalatest.{MustMatchers, PropSpecLike}

trait BaseSpec[F[_]]
    extends KontextfreiSpec[F]
    with DistributionSyntaxSupport
    with PropSpecLike
    with GeneratorDrivenPropertyChecks
    with MustMatchers {

  implicit val config: PropertyCheckConfiguration =
    PropertyCheckConfiguration(minSuccessful = PosInt(100))
} 
```

Enter fullscreen mode Exit fullscreen mode

像我们的`WordCount`特征一样，`BaseSpec`接受一个类型构造函数，并简单地将其传递给`KontextfreiSpec`特征。我们一会儿会回到这个话题。

我们实际的测试属性现在可以为任何类型构造函数`F[_]`实现，对于它有一个`DCollectionOps`的实例。我们在特征`WordCountProperties`中定义它们，该特征也必须由类型构造函数进行参数化:

```
package com.danielwestheide.kontextfrei.wordcount

trait WordCountProperties[F[_]] extends BaseSpec[F] with WordCount {

  import collection.immutable._

  property("sums word counts across lines") {
    forAll { (wordA: String) =>
      whenever(wordA.nonEmpty) {
        val wordB = wordA.reverse + wordA
        val result =
          counts(Seq(s"$wordB $wordA $wordB", wordB).distributed)
            .collectAsMap()
        assert(result(wordB) === 3)
      }
    }
  }

  property("does not have duplicate keys") {
    forAll { (wordA: String) =>
      whenever(wordA.nonEmpty) {
        val wordB = wordA.reverse + wordA
        val result =
          counts(Seq(s"$wordA $wordB", s"$wordB $wordA").distributed)
        assert(
          result.keys.distinct().collect().toList === result.keys
            .collect()
            .toList)
      }
    }
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们希望能够针对快速 Scala 集合以及本地 Spark 集群中的`RDD`测试我们的 Spark 不可知属性。为此，我们需要定义两个测试类，一个在`test` sources 目录中，另一个在`it` sources 目录中。下面是单元测试:

```
package com.danielwestheide.kontextfrei.wordcount

import com.danielwestheide.kontextfrei.scalatest.StreamSpec

class WordCountSpec extends BaseSpec[Stream]
  with StreamSpec
  with WordCountProperties[Stream] 
```

Enter fullscreen mode Exit fullscreen mode

我们混合了`BaseSpec`并传递给它`Stream`类型构造函数。`Stream`外形和`RDD`一样，但是是 Scala 集合。由`BaseSpec`扩展的`KontextfreiSpec`特征为其类型构造函数定义了一个抽象隐式`DCollectionOps`。通过混合`StreamSpec`，我们得到了`Stream`的一个`DCollectionOps`实例。当我们实现我们的业务逻辑时，我们可以运行`WordCountSpec`测试并获得即时反馈。我们可以使用 SBT 的触发执行，并使用`~test`让它在每次检测到源代码变化时运行我们的单元测试，这样会非常快。

为了确保只有在 Spark 集群中才会注意到的典型 bug 不会潜入，我们还定义了一个集成测试，它测试完全相同的属性:

```
package com.danielwestheide.kontextfrei.wordcount

import com.danielwestheide.kontextfrei.scalatest.RDDSpec
import org.apache.spark.rdd.RDD

class WordCountIntegrationSpec extends BaseSpec[RDD]
  with RDDSpec
  with WordCountProperties[RDD] 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们混合了`RDDSpec`，因为我们用`RDD`类型构造函数传递参数化`BaseSpec`。

## 设计目标

尽可能紧密地坚持现有的 Spark API 是一个明确的设计目标，允许拥有现有 Spark 代码库的人尽可能平稳地切换到 *kontextfrei* ，甚至在没有太多麻烦的情况下迁移他们应用程序的一部分，好处是现在能够用遗漏的测试覆盖他们的业务逻辑，而没有通常的痛苦。

当然，这种方法的另一种选择是基于流行的解释器模式来构建这个库。老实说，我希望 Spark 本身也使用这种模式 Apache Crunch 等其他库已经成功地表明，这可以极大地帮助开发人员为他们的应用程序的业务逻辑编写测试。如果 Spark 建立在这些原则之上，那么 kontextfrei 就没有任何存在的理由了。

## 局限性

*kontextfrei* 仍然是一个年轻的库，虽然我们已经在一个项目的生产中使用了它，但我不知道还有其他的采用者。它的一个局限性是它还不支持在`RDD`类型上定义的所有操作——但是我们已经越来越接近了。此外，我还没有找到一个聪明的方法来支持广播变量和累加器。当然，2017 年还有谁在用`RDD` s 呢？虽然我确实认为基于`RDD`的 Spark 应用程序仍有发展空间，但我知道许多人早就转向了`Dataset`和 Spark 流媒体。为数据集和流应用程序创建一个类似的基于类型类的抽象会很好，但是我没有时间深入研究实现这两者的必要条件。

## 总结

*kontextfrei* 是一个 Scala 库，旨在开发 Apache Spark 应用时为开发者提供更快的反馈循环。为了实现这一点，它使您能够编写 Spark 应用程序的业务逻辑以及测试代码，而不是 Spark RDD 上的抽象。

我很想听听你对这种方法的想法。你认为为了测试的目的，定义有史以来最大的 typeclass 并重新实现 Scala 集合的`RDD`逻辑值得吗？请，如果这看起来有趣，一定要尝试一下。我总是对反馈和各种贡献感兴趣。

## 链接

*   无上下文项目网站
*   [kontextfrei GitHub 回购](https://github.com/dwestheide/kontextfrei)
*   [kontextfrei 字数示例](https://github.com/dwestheide/kontextfrei-wordcount)