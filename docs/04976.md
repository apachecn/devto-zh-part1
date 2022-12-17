# Rust 入门:演示和安装

> 原文：<https://dev.to/mnivoliez/getting-started-with-rust-presentation-and-installation>

*最初发布在我的[博客](https://mathieu-nivoliez.com/posts/2017-09-09-getting-started-with-rust-presentation-and-installation/)上。*

大家好，今天我们来谈谈铁锈。“你经常提起这件事，是吗？”

的确，更有理由适当地介绍它，对吗？“好的，我们在听。”

首先要做的事。Rust 是一种编译程序语言。这意味着你写的代码将被翻译成另一种计算机实际上能理解的代码。这个翻译将被称为可执行文件或库(或者更一般地，工件)。Rust 也是一种强类型语言。换句话说，变量是某种类型的。我们下次再谈。最后，在[官方网站](https://www.rust-lang.org/en-US/)上，Rust 被描述为:

> Rust 是一种系统编程语言，运行速度极快，可以防止 segfaults，并保证线程安全。

“赛格福特？线程安全？”

如果您从未使用过任何“低级”语言，如 C 或 C++，这些术语对您来说肯定是新的。我将尝试在这里快速定义它们，但我们将在其他一些帖子中更详细地讨论。

Segfault 或分段错误是指程序试图访问它无权访问的内存。就像在你的工作中，你有一张办公桌，但你试图用你同事的，同样的，你的同事不喜欢，电脑也不喜欢。然后程序崩溃了。

和线程...我们也称它们为“轻量级过程”。他们可以和其他人并行工作。你可以把它想象成一堆要洗的盘子。你可以用更多的人来洗碗。风险在于两个或更多的人试图同时清洗同一个盘子。

防锈防止这些问题，你会看到在这些文章的过程中。“好吧，但还是很灰暗……”

没有什么比这更正常的了，这些观念很难理解，它迟早会到来。所以别担心，开心点。“好吧，我们开始吧？”

当然啦！我们要做的第一件事是安装 Rust。为此，我们将使用工具 [Rustup](https://rustup.rs/) 。从现在开始，您将需要一个终端。

如果您使用的是 Linux 或 Mac，这个命令应该足够了:

`curl https://sh.rustup.rs -sSf | sh`

如果您使用的是 Windows，请访问 Rustup 的[网站下载该实用程序，并按照其说明进行操作。](https://rustup.rs/)

现在，该使用货物了！
“货物？”

这是一个管理 Rust 项目的工具。进入你最喜欢的目录(不是那个大的小脏东西，是你的项目目录):

对于 Linux

`cd ~/workspace`

对于 Windows

`cd %USERPROFILE%\projects`

然后

`cargo new hello_world --bin
cd hello_world`

标志`--bin`告诉 to Cargo 生成一个可执行文件。如果没有此选项，该命令将为库创建配置。

在新创建的文件夹中，您可以看到一个名为`src`的目录，它保存我们的源代码，还有一个名为`Cargo.toml`的文件，它包含项目信息:

```
[package]
name = "hello_world"
version = "0.1.0"
authors = ["Mathieu Nivoliez"]

[dependencies] 
```

Enter fullscreen mode Exit fullscreen mode

*   名字
*   版本
*   作者
*   属国

现在，看一下`src` :
中的文件`main.rs`

```
fn main() {
    println!("Hello, world!");
} 
```

Enter fullscreen mode Exit fullscreen mode

“我们应该怎么读呢？”

我会帮助你的，别担心。首先，我们得到了一个名为`main()`的函数`fn`。当被调用时，它将执行宏`println!`，带有“你好，世界！”作为参数，它将打印“Hello，world！”在控制台里。

为了编译和执行程序，运行 cargo run。

函数和宏是很大的话题，所以我们后面会讲到。

最终，它没有....抱歉，最后我们安装了 Rust 并使用 Cargo 创建了一个 hello world 项目。

稍后在另一篇关于如何开始使用 Rust 的文章中再见。

马修

来源:[《书》](https://doc.rust-lang.org/book/second-edition/ch01-00-introduction.html)