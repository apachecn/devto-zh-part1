# 有用的 Golang 包和工具

> 原文：<https://dev.to/z0al/useful-golang-packages-and-tools-6aa>

> 封面图片来自:[https://golang.org/doc/gopher/biplane.jpg](https://golang.org/doc/gopher/biplane.jpg)

我最近编写了我的第一个围棋语言程序🙌。在这篇文章中，我想分享一些我认为非常有用的 Go 软件包。

所以事不宜迟，让我们开始吧:)

#### GoReleaser

为几个平台构建和分发二进制文件可能很有挑战性。GoRelaeser 帮助您轻松地自动交付应用程序二进制文件。它可以生成归档的发布文件(即二进制文件+许可证+自述文件)、校验和、自制 tap、Docker 映像、[快照包](http://snapcraft.io)、发布到 GitHub(包括生成可定制的 changelog😃)等等。

它与特拉维斯和 T2 配合得很好，所以你可以自动化几乎所有的事情👌

🔗[http://github.com/goreleaser](http://github.com/goreleaser/)

#### 眼镜蛇

Cobra 既是一个用于创建强大的现代 CLI 应用程序的库，也是一个用于生成应用程序和命令文件的程序。Cobra 为您提供了构建 awesome CLI 所需的一切，包括子命令、自动使用消息、运行前和运行后挂钩，以及生成手册页/Markdown/ressstructuredtext 文档。

此外，当“未知命令”发生时，它提供自动(无需任何配置)建议，例如(是，[雨果](https://gohugo.io/)由眼镜蛇供电):

```
$ hugo srever
Error: unknown command "srever" for "hugo"

Did you mean this?
        server

Run 'hugo --help' for usage. 
```

Enter fullscreen mode Exit fullscreen mode

🔗[https://github.com/spf13/cobra](https://github.com/spf13/cobra)

#### 调查

你爱 Node.js [问询者包](https://www.npmjs.com/package/inquirer)吗？如果是，那么你可能也会喜欢调查。虽然它还不支持[问询者](https://www.npmjs.com/package/inquirer)提供的所有功能，但它拥有所有的基本功能，并且非常易于使用。

<figure>[![](img/8fae9dcd5403993962ce3812a69eaa7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qfXiIL9I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/556/1%2A4XiP6M9kOShQz7f1YQxBOg.gif) 

<figcaption>调查包在行动</figcaption>

</figure>

🔗[https://github.com/AlecAivazis/survey](https://github.com/AlecAivazis/survey)

#### 粉笔

同样，如果你熟悉像[粉笔](https://www.npmjs.com/package/chalk)这样的 [npm](http://npmjs.org) 包，你会在这里有家的感觉。Chalk 是一个用于设计控制台/终端输出的 go 包。像这样:

[![](img/14bb0c9488a3b299ead8708f5073b079.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2bHKRGfw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_Ury3oCOvS_-3keggOMNLA.png)

🔗[https://godoc.org/github.com/ttacon/chalk](https://godoc.org/github.com/ttacon/chalk)

#### go-gitconfig

可能是所有包中最小的，但是我发现它在 git 相关的项目中非常有用。当您想从全局~/中提取一些信息时，这很有帮助。gitconfig 或本地。git/config 文件。下面是我用来从 GitHub 提取远程问题的原始 URL 值的一部分代码:

```
import (
    config "github.com/tcnksm/go-gitconfig"
)

remote := "origin"
url, err := config.Local(fmt.Sprintf("remote.%s.url", remote))

if err != nil {
    // .. print error
}
// use "url" value here to fetch from GitHub 
```

Enter fullscreen mode Exit fullscreen mode

🔗[https://github.com/tcnksm/go-gitconfig](https://github.com/tcnksm/go-gitconfig)

#### 其他环节

1.  [令人敬畏的 Go 框架、库和软件的精选列表](https://github.com/avelino/awesome-go)
2.  @plutov 的实用(标准)围棋工具列表
3.  [我用 Go 编写的简单的基于 Git 的待办事项应用程序](https://git.io/todos)

我希望这篇文章对你有用。谢谢大家；)

* * *

最初发布在[我的博客](https://medium.com/ahmed-t-ali/useful-golang-packages-and-tools-f55b9986a041)