# 理解固体原理:界面分离原理

> 原文：<https://dev.to/theodesp/understanding-solid-principles-interface-segregation-principle-901>

[![pexels](img/89a57aa315a307e34526918471598096.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rWdfvDVh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ebbp96owlrswxsz37ish.jpeg)

[**阅读原文**](https://codeburst.io/understanding-solid-principles-interface-segregation-principle-b2d57026cf6c)

> 这是理解坚实原则系列的第 4 部分，我们将探讨什么是接口分离原则，以及为什么它有助于创建瘦抽象接口，使客户端之间的依赖因素更少。

作为一个小提醒，在 **SOLID** 中有五个基本原则可以帮助创建好的(或坚实的)软件架构。SOLID 是一个缩写词，其中:-

**S** 代表 SRP(单一责任原则)
**O** 代表 OCP(开闭原则)
**L** 代表 LSP(利斯科夫替代原则)
**I** 代表 ISP(接口分离原则)
**D** 代表 DIP(依赖倒置原则)

我们之前讨论过[依赖倒置](https://codeburst.io/understanding-solid-principles-dependency-injection-d570c15560ab0)、[单一责任](https://codeburst.io/understanding-solid-principles-single-responsibility-b7c7ec0bf80)和[开闭原则](https://medium.com/r/?url=https%3A%2F%2Fcodeburst.io%2Funderstanding-solid-principles-open-closed-principle-e2b588b6491f)。

现在，我们将解决接口隔离问题。

## 界面偏析原理

> 客户不应该被迫依赖他们不使用的方法。

这一原则与单一责任原则密切相关。它真正的意思是，你应该总是以这样一种方式设计你的抽象，即使用公开方法的客户不会得到整个饼。**这也包括让客户承担实现他们实际上并不需要的方法的负担**。

保持你的接口薄或细粒度，不要附加未使用的方法。你将不得不更加机智的命名，因为你将不得不命名更多的类型。这很好，因为如果需要的话，它可以让您灵活地将较薄的界面组合成较大的界面。

我们来看一个围棋的例子。假设我们有一个实用函数库，它处理我们想要公开的字节操作。我们创建接口:

```
type ByteUtils interface {
    Read(b []byte) (n int, err error) // Read into buffer
    Write(b []byte)(n int, err error) // Write into buffer
    Trim(b []byte, exclusions string)[]byte // Trim buffer by removing bytes from the exclusion chars
} 
```

Enter fullscreen mode Exit fullscreen mode

暂时有效。但是，请注意，ByteUtils 这个名称过于笼统，它没有提供任何具体信息——它可以被称为任何名称。此外，实现所需的方法非常困难，因为如果在读入缓冲区时和写入缓冲区时需要进行不同的优化，则需要提供两种不同的 ByteUtils 实现，这两种实现的读取和写入都是相同的。不好！

相反，我们可以提供一个简单的接口列表:

```
type Reader interface {
    Read(b []byte) (n int, err error) 
}
type Writer interface {
    Write(b []byte)(n int, err error) 
}
type Trimmer interface {
    Trim(b []byte, exclusions string)[]byte 
} 
```

Enter fullscreen mode Exit fullscreen mode

这种瘦接口也被称为**角色接口**，因为它更容易重构、更改和重新部署具有明确角色或目的的东西。
在这种情况下，我们可以组合一个更相关的接口列表，如下所示:

```
type ReadWriter interface {
    Reader
    Writer 
}
type TrimReader interface {
    Trimmer
    Reader
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得客户可以灵活地组合他们认为合适的抽象，并在没有不必要的负担的情况下提供实现。在上面的例子中，客户端可以提供优化的 TrimReader 的实现。

## 结论

正如你所看到的，胖接口导致了类之间的无意耦合，你应该避免它们。设计接口时，你应该经常问自己这样一个问题“我真的需要我正在使用的这个接口上的所有方法吗？如果没有，我如何将它们分解成更小的接口？”。

正如甘地当时所说:

> “你的行为成为你的习惯，你的习惯成为你的价值观，你的价值观成为你的命运。”

如果你每时每刻都做正确的事情，并且只允许好的模式出现在你的架构中，这将成为一种习惯。它会成为你的价值观或原则。如果你坚持这样做，这将成为你的命运，那就是总是产生合理的解决方案或者你心目中的软件工程师。

我的观点是通过挑战自己来努力变得更好。在某些时候，你会问，但你已经知道答案了…

编码快乐！

## 参考文献

*   [维基页面](https://medium.com/r/?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FInterface_segregation_principle)
*   [C2 页面](https://medium.com/r/?url=http%3A%2F%2Fwiki.c2.com%2F%3FRolePattern)

## 接下来是理解固体原理:利斯科夫替代原理

**如果这篇文章有帮助，请分享并关注我的其他[文章](https://medium.com/@fanisdespoudis/)。你可以在 [GitHub](https://github.com/theodesp) 和 [LinkedIn](https://www.linkedin.com/in/theofanis-despoudis-7bb30913/) 上关注我。如果你有任何想法和改进，请随时与我分享。**

快乐编码。

**如果您想安排一次指导会议，请访问我的[共同导师简介](https://www.codementor.io/theofanisdespoudis)** 。