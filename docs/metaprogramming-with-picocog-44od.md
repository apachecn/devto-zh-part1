# 代码生成很容易——使用 Picocog

> 原文：<https://dev.to/ainslec/metaprogramming-with-picocog-44od>

Picocog 是一个轻量级的新的开源 Java 库，旨在使编程代码生成(或元编程)变得干净、容易实现。

代码生成是基于输入(也称为模型)以编程方式生成源代码的过程。

代码生成通常按如下方式工作:

*   定义模型/使用现有模型。
*   询问模型并发出/生成源代码
*   (可选)当模型发生变化时，重新生成源代码。

# 普通的旧 Java 对象

为利用 Picocog 而设计的代码生成器通常利用 POJO 模型作为模型数据源。

替代用例是通过 Java 的 JSR-269 特性([链接](https://deors.wordpress.com/2011/10/08/annotation-processors/))使用带注释的 Java 源代码。

为了简单起见，本文将假设 PoJo 场景。

下面显示了一个示例 POJO 模型(为简洁起见，省略了 getter/setter)。

[![Sample PoJo Model](img/d9b6940fcf715962da5d648cae3c966c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_YM4ZRrQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fopn6ml21s33vjjmaunc.png)

这种 POJO 模型通常由以下类型的源实例化:

*   JSP/XML/YAML/CSV
*   通过数据库查询
*   [DSL](https://en.wikipedia.org/wiki/Domain-specific_language) (领域特定语言)

例如:

[![XML representation of the model](img/bd26a95d9822e701e291ac161d68d0cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uvExmhrH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x4zdrn01ldkifizxna4b.png)

注意:在实例化 POJO 模型时，执行全套验证并测试假设是很重要的。XML 模式或定制的 DSL 实现可以确保引用完整性和其他验证。

# 代码生成

Picocog 通过 Java 代码发出文本。没有模板，没有反射，没有标记处理。它有三个核心目标:

*   轻松生成整洁的缩进代码。
*   支持无序线注入。
*   易于调试。

# 实践中的微微齿

Picocog 可以被认为是 StringBuilder 的一个以代码生成为中心的替代方案。

Picocog 只是简单地写几行——记住当前的缩进级别。

唯一需要的类是 PicoWriter 类。

不需要手动将缩进放入字符串文字中，但是需要通过 writeln_r()、writeln_l()和 writeln_lr()方法标记左缩进和写代码行。

有时，想要同时写入文档中的两个或更多位置，并让这些位置拥有自己的缩进堆栈是很方便的。为此，您可以通过 createDeferredWriter()方法添加一个占位符。

返回的编写器可以看作是指向创建它的那一行的指针。

# 微微齿用法举例

代码生成源代码:
[![The code generation source code](img/7795bec85b05d0d23e15fe1859257008.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r6c2RS8V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dllnl00q0uflpb357ugg.png)

# 生成类

*Customer.java(由 Picocog 生成):*

[![Customer.java](img/ad08ec2ca3698863f2a83c4c5460b044.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1a6iJ0Ma--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kfcgwhgo76isdiijei9y.png)

*Order.java(由 Picocog 生成):*

[![Order.java](img/c6d55aefe6428d50d4370383fb466833.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nE3Gczd5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n97rdwhv345bb0p5n8rk.png)

# 逃跑

Picocog 的初始版本不支持任何类型的文本转义。要么写你自己的转义代码(相当容易)，要么利用库，比如 [Commons Lang](https://commons.apache.org/proper/commons-lang/) 来满足你的[文本转义需求](https://commons.apache.org/proper/commons-lang/javadocs/api-3.1/org/apache/commons/lang3/StringEscapeUtils.html)。

# 语言支持

Picocog 是用 Java 写的，但是它可以用任何语言发出代码。

可以创建代码生成器，使得 C#、Java、JavaScript 等可以从单个模型中发出，包括附加到 Java 代码的注释(通过 JSR 269)。

有时一个小的实现就足够了。

# 链接

Picocog 今天可以在 GitHub 上下载。

另一种基于非模板的代码生成方法是优秀的 [xtend](https://www.eclipse.org/xtend/) 语言。

关于代码生成的更深入的讨论，请参阅 Matthew Fowler 的优秀文章“[傻瓜代码生成](http://www.methodsandtools.com/archive/archive.php?id=86)”。

注:本文改编自(同一作者)以下关于 medium.com 的[博客文章](https://medium.com/p/introducing-picocog-68de4978eaf4)