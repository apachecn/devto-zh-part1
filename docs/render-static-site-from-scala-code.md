# 从 Scala 代码呈现静态站点

> 原文：<https://dev.to/sake_92/render-static-site-from-scala-code>

你有没有想过:

> 如果我丑陋的 HTML 中有一个变量...这样我就不必重复所有那些引导类，并且可以在一个地方改变它们...

# [Sbt-hepek](https://github.com/sake92/sbt-hepek) 前来救援

所以，经过一番思考，我决定为此编写我的第一个 sbt 插件( [Sbt](http://www.scala-sbt.org/) 是一个 [Scala](http://www.scala-lang.org/) 构建工具，就像 Java 生态系统中的 Maven 或 Gradle)。

注意:*这是**而不是**一个标准的基于模板的、特殊语言的静态站点生成器，就像 Jekyll 和其他人一样。这是一种**完全**不同的方法，也就是说，我们使用 Scala `object` s 来表示页面。我们可以利用 Scala 的一些特性:*

*   Scala 将单例对象作为内置特性。每个扩展了`Renderable`特征的`object`都将被渲染到一个文件中。您可以指定默认扩展名等。
*   还有，Scala/Java 包可以用来*表示*文件系统结构！例如，如果一个`object`在一个`package mysite.posts`中，它可以被渲染到一个“mysite/posts”文件夹中！很酷，对吧？:)

您页面的主要内容可能是这样的:

```
 object HelloWorld extends JavaTemplate {

  override def pageTitle = "Hello world!"

  val introSectionContent =
    div(
      p("Java "Hello World!" example:"),
      javaSnippet("""         
            class HelloWorldApp {
                public static void main(String[] args) {       
                    System.out.println("Hello!");      
                }
            }
      """)
    )

  val introSection = Section("Hello world!", introSectionContent)
  override def sections = Seq(introSection)
} 
```

Enter fullscreen mode Exit fullscreen mode

# 思想

由于我们使用全功能的编程语言，我们可以做许多用定制的模板语言如 Markdown 做不到的事情。这些是已经实施的一些想法。

### 模板

很明显，为了表示一个模板，你可以使用`trait` s。然后你创建一个`object`来扩展它，并且只实现`override`那些`abstract`的方法。Scala 的 trait 方法可以有默认实现，所以我们也可以利用这一点。

### 相对路径

我想到的另一件事是 HTML 中容易出错的相对路径，比如`../js/something.js`。我们可以创建一个方法`def relTo(other: RelativePath): String`来为我们“计算”它。然后一个相对的链接看起来像`Index.relTo(MyOtherPage)`，或者来自`Index`对象本身`this.relTo(MyOtherPage)`。很直观的 IMHO。

### 章节

这个是从李的 [Scalatex](http://www.lihaoyi.com/Scalatex/#Section) 里“偷”来的。想法很简单，将你的内容组织在[章节](https://github.com/sake92/sake-ba-source/blob/master/core/src/main/scala/hepek/templates/Section.scala#L9)中，这样你就可以[将它们](https://github.com/sake92/sake-ba-source/blob/eea293d665611415b7d6ec30d3cdc5566c16c155/core/src/main/scala/hepek/utils/HTMLUtils.scala#L48)呈现为一本书中的章节。此外，您可以制作一个漂亮的目录！:)

# 示例站点

完整的例子，请看我的[回购](https://github.com/sake92/sake-ba-source)还活着@ [blog.sake.ba](https://blog.sake.ba)

它有一个模板所在的`core`项目(如果我决定创建另一个网站，我可以重用它)，和内容所在的`sake-ba-blog`。

# 结论

依我看，对于静态网站和各种静态的东西，比如 XML 或 JSON，这是一个非常强大的方法。由于没有解析，我们可以通过检查类的依赖关系来递增地呈现文件。例如，如果我们改变一个模板，我们希望所有的`Renderable`被再次渲染，但是如果我们仅仅改变一个页面的内容，那么只有那个页面应该被渲染。实施细节见 [hepek-core](https://github.com/sake92/hepek-core) 。

这种方法可以在其他将单例对象作为一级实例的语言中实现，比如 Kotlin 和其他语言。
欢迎意见、建议！:)