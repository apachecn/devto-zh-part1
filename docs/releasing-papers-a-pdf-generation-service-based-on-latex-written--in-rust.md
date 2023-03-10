# 发布论文，一个基于 LaTeX 的 PDF 生成服务，用 Rust 编写

> 原文：<https://dev.to/tomhoule/releasing-papers-a-pdf-generation-service-based-on-latex-written--in-rust>

store2be 团队很高兴地宣布[论文](https://github.com/store2be/papers)的[首次公开发布](https://github.com/store2be/pape-rs/releases/tag/0.1.0)，这是一项 HTTP 服务，可以接收生成 LaTex 文件所需的 LaTeX 模板、变量和资产，并生成好看的 pdf。它被设计成无状态、安全、可靠、易于使用和部署。我们甚至有第二个二进制文件，在部署模板之前在本地测试它们。

## 什么？为什么？

我们需要在 store2be 为我们的广告空间管理器中的新功能提供 PDF 生成服务。我们调查了四个选项:

*   使用像[大虾](http://prawnpdf.org/)这样的纯 Ruby PDF 库
*   用类似于 [wkhtmltopdf](https://wkhtmltopdf.org/) 的东西从 HTML 和 CSS 生成 pdf
*   使用像 [DocRaptor](https://docraptor.com/) 这样的付费服务
*   我们自己写

对于我们可能要编写的模板数量来说，将模板编写为代码花费的时间太长了，而且我们自己从 HTML 生成的文档也不太好，所以剩下的解决方案是外部服务或论文。鉴于这个项目的范围很小，我们将从使用不同的编程生态系统的实验中学到什么(我们以前只在后端使用 Ruby)，以及 TeX 制作的更好看的 pdf，我们继续写论文。

## 我怎么用？

你需要三样东西:

*   让 Papers 服务在某个地方运行，这样就可以对它进行 HTTP 调用
*   将( [Tera](https://github.com/Keats/tera) )模板和资产托管在某个地方(我们使用 S3/CloudFront)
*   在你的应用程序中有一个 HTTP 端点，在那里发布 PDF 或错误

生成 PDF 是一件简单的事情，只需向 Papers 服务的`/submit`端点提交一个描述您的愿望的 JSON 对象。例如:

```
{  "assets_urls":  [  "https://static.cook.example.com/spaghetti.jpg",  "https://static.cook.example.com/pesto.jpg"  ],  "callback_url":  "https://cook.example.com/illustrated-recipes/generated&token=18e8d495ac34c541b5a5167bffcbac96",  "template_url":  "https://static.cook.example.com/templates/pesto_spaghetti.tex"  } 
```

为了方便部署论文，我们准备了一张 [docker 镜像](https://hub.docker.com/r/store2be/pape-rs/)。存储库中还有一个 [Kubernetes 的例子](https://github.com/store2be/pape-rs/tree/master/examples/kubernetes)。

## 我们对铁锈的体验

总的来说，Rust 非常适合我们的目标。

*   我们想要一个无崩溃的服务，而显式的错误处理使这一点变得可能，并且有很高的可信度。

*   我们希望服务稳定，不会泄漏内存、磁盘空间或文件描述符。Rust 为您提供了应用相同逻辑的工具，使内存安全和其他资源(如文件或临时目录)的垃圾收集成为可能，这再次非常有帮助。我们还通过 tokio 获得了细粒度的并发控制。

*   由于服务的大部分开销来自 LaTeX 本身，我们希望它尽可能的轻量级，主要是内存方面，与 Ruby 相比，Rust 又是理想的。

*   我们想要简单的部署和管理，而拥有一个编译过的二进制文件使这变得非常容易。

编写一个小服务也是我们团队评估 Rust 作为未来其他项目工具的一种方式。从这次经历中，我们的观念在很多方面都有所调整。

我们认为 Rust 是一个很酷的新事物，并期望从中获得高层次的抽象。团队中以前使用 Rust 的经验从阅读书籍到完成小程序不等，学习曲线比我们想象的要大。rust*是否迫使你从更低的层面思考，处理比 Ruby 或 Python 更多的细节:你必须对错误做出决定，即使你想忽略它们，即使有办法简化内存管理问题(例如`Rc`)，它们也是选择加入的。*

 *很难接受的是，有时您必须编写代码来编译程序，而这对解决业务问题毫无帮助(在我们习惯于高级语言的程序员看来)，例如:

```
fn make_pasta(&self) -> Box<Future<Item=Satisfaction, Error=OvercookedError>> {
  let logger = self.logger.clone();
  Box::new(cook_pasta.and_then(move |pasta| {
    warn!(logger, "Pasta is very hot, be careful");
    eat_pasta(pasta)
  }))
} 
```

我们克隆日志记录器以便它可以被移动到将来，这纯粹是为了满足借用检查器，虽然一旦您“获得”概念所有权，它就完全有意义，但在高级代码的上下文中，它仍然感觉像是杂乱的。

货运生态系统，尤其是货运码头，非常棒。句号。

就库而言，我们为所有内容都找到了高质量的库。现在，服务器编程中的大象当然是生态系统到基于 [futures](https://github.com/alexcrichton/futures-rs) 和 [tokio](https://tokio.rs/) 的异步 API 的正在进行的转换，这很不方便，但并不是一个阻碍。我们选择了主分支机构的 hyper，因为没有更高级别的替代方案适用于期货。

总的来说，未来是艰难的。有明显的向右漂移，错误处理不同于同步代码(以一种不好的方式)。与事件循环的交互也有自己的学习曲线。公平地说，随着时间的推移和一些已经计划好的语言特性，事情将会有很大的改善，特别是 [impl Trait](https://github.com/rust-lang/rfcs/pull/1951) 、 [trait 别名](https://github.com/rust-lang/rfcs/pull/1733)，以及更遥远的 async/await 语法。当我们不得不返回未来时，当前对装箱所有东西或编写复杂类型的需求将我们推向了大的、单片函数的方向。

编译时间很长。来自解释语言，这是在 Rust 中开发最令人沮丧的部分。尽管它们正在变得更好，增量编译已经为我们带来了有希望的结果。

我们的结论并不是很新颖:Rust 对于有意义的用例来说是一个很好的工具，但是我们不会用它作为一种语言来编写一个经典的 web 应用程序。

## 接下来是什么？

该服务可用于小批量 pdf(取决于大小，每分钟 12 个似乎是合理的)和防火墙后的生产。我们确实计划让它变得更快，在这个方向上第一个明显的步骤是缓存资产。我们还需要一种更智能的方法来管理 latex 流程，以及测量和提高性能的基准。

在下一个版本中，已经有许多其他特性朝着更好的可配置性和更好的调试体验的方向发展。

## 后记

如果你读到这里，我们真的对我们的努力和代码的反馈感兴趣，如果你有的话。请给我们发一封电子邮件，地址:[tech@store2be.com](mailto:tech@store2be.com):)

要获得全面的文档，您可以阅读[自述文件](https://github.com/store2be/pape-rs/README.md)。

这当然都是开源的，在 MIT 许可下发布，在 Github 上也有[。我们非常欢迎捐款。](https://github.com/store2be/pape-rs)*