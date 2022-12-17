# Sentri.io —错误处理

> 原文：<https://dev.to/gimlet2/sentriio--errors-handling-536p>

今天我想谈谈上周我自己发现的一项服务。正如你可能已经从文章标题中得知的，它是 [Sentry.io](https://sentry.io/) 。这个开源项目帮助您收集和监控关于应用程序中发生的错误的信息。除了开源版本，还有托管的[付费](https://sentry.io/pricing/)(有免费爱好者计划)版本，应该是一样的。

该服务提供了对不同编程语言的广泛支持，如 Java、C#、Elixir、Go、Javascript、Node、Perl、PHP、Python 等。集成过程非常简单——您只需要在项目中添加适当的依赖项，并提供一点点[配置](https://docs.sentry.io/clients/java/config/)(至少是访问令牌)，然后就可以开始了。

在我的例子中，它是一个带有**日志返回**日志框架的**弹簧启动**应用程序。这两种技术都受到 Sentry.io 的支持。因此，我将 **io.sentry:sentry-spring** 和 **io.sentry:sentry-logback** 库放入项目 **Gradle** 脚本中。然后，我在 Sentry 的仪表板中创建了一个新项目，并为它生成了一个令牌。下一步—项目内部的配置。这就是我不喜欢这个故事的原因。也许有办法，但我找不到——所以，我不得不复制配置两次——分别用于 logback 和 spring-boot。我已经在项目的资源文件夹中添加了 **sentry.properties** 文件，里面有 **dsn** 键，还有 **sentry.dsn** 键到我的 **application.yaml** 。此外，用以下块更新了回退附加器的配置: