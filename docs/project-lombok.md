# 你知道龙目岛计划吗？

> 原文：<https://dev.to/ebud7/project-lombok>

我使用和欣赏 Lombok 已经有一段时间了。这是我放入 Java 项目的第一个依赖项。这篇文章只是让你好奇写更少，更干净的代码，当然节省一些时间。

-什么

# 什么样的问题解决了 Project Lombok？ðŸ'€

嗯，我认为这个报价是钉它。

> “样板文件”是一个用来描述代码的术语，这些代码在应用程序的许多部分重复出现，几乎没有改动。对 Java 语言最常见的批评之一是大多数项目中这种类型代码的数量。这个问题经常是各种库中设计决策的结果，但是由于语言本身的限制而加剧。Lombok 项目旨在通过用一组简单的注释来替换一些最糟糕的违规者，从而减少它们的流行。
> 
> *   迈克尔·金伯利

# 给我看看一些代码！ðŸ™

```
package com.codebud7.example;

import lombok.Builder;
import lombok.Getter;
import lombok.ToString;
import lombok.extern.java.Log;

@Log // or: @Slf4j @CommonsLog @Log4j @Log4j2 @XSlf4j
public class LombokDemoApplication
{
    public static void main(String[] args)
    {
        Article article = Article.builder()
            .title("Project Lombok")
            .published(false)
            .description("It's magic!")
            .build();

        log.info(article.toString());
    }
}

@Getter
@Builder // or: @Setter
@ToString
class Article
{
    private String title;
    private Boolean published;
    private String description;
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是一些强大的 Lombok 特性的一个小例子。

在 LombokDemoApplication 类的顶部，您可以看到`@Log`注释。Lombok 并没有声明你所选择的 logger 框架的实例，而是为你保存公共声明。

让我们来看看文章类。使用`@Getter`，你将永远不会再写
`public int getFoo() {return foo;}`。`@Builder`注释为您的类生成复杂的构建器 API，并且`@ToString`按顺序打印您的类名以及每个字段，默认情况下用逗号分隔。

# 下一步ðÿž”

因此，如果你很好奇，想知道更多关于龙目岛项目的细节，你应该访问以下网站:

ðÿ'‰[龙目岛官方项目文档](https://projectlombok.org/index.html)
ðÿ'‰[贝尔东对更多龙目岛特性的精彩介绍](http://www.baeldung.com/intro-to-project-lombok)